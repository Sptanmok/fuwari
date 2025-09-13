---
title: WireGuard网络隧道+假TCP+Nginx反向代理实现HTTP3教程
published: 2025-06-29
description: "本网站已搭载http3（仅在http响应头头部servernode信息不包含frp时才支持，因为目前frp尚未支持http3），本文中源站主机默认指没有独立ip为nat的主机 在跳板机安装支持HTTP [&hellip;]"
tags: []
categories:  教程 服务器/端 软件
---
本网站已搭载http3（仅在http响应头头部servernode信息不包含frp时才支持，因为目前frp尚未支持http3），本文中源站主机默认指没有独立ip为nat的主机

在跳板机安装支持HTTP3的Nginx并配置
----------------------

nginx在1.25.0开始支持HTTP3，所以你需要安装≥1.25.0版本的Nginx，如果你已经安装了≥1.25.0版本的Nginx版本可以跳过下载安装步骤

这些版本通常不会在linux发行版软件包内(至少大部分版本)，所以你一般无法直接通过apt或yum命令安装

所以本文讲述从源码编译下载安装Nginx

本文以nginx1.26.3为例，如果你想要其他版本可以到[nginx：下载](https://nginx.org/en/download.html)去获得其他版本，然后在文中替换相关内容即可  
如果你的主机内已经安装了nginx，可以输入nginx -V复制configure arguments:后面的内容，以延续nginx之前的配置

在linux主机（新建一个文件夹），然后进入

输入

```
wget https://nginx.org/download/nginx-1.26.3.tar.gz
```

回车，下载Nginx

然后输入

```
tar -zvxf nginx-1.26.3.tar.gz
```

回车，解压压缩包

输入

```
cd nginx-1.26.3
```

回车，进入解压出的文件夹内

如果你已经安装了Nginx，可以输入

```
./configure 刚才复制的内容 --with-http_v3_module
```

如果你还没有安装Nginx，可以输入（这段配置已经提供大多数必要的组件，如有需要可以再添加，添加–prefix=/usr/local/nginx是为了讲解方便）

```
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_sub_module --with-http_ssl_module --with-http_v2_module --with-http_v3_module --with-http_realip_module
```

如果提示错误缺什么的时候你就用安装所需软件即可，以下安装命令包含了大部分nginx所需的软件

```
sudo apt install build-essential libpcre3 libpcre3-dev zlib1g zlib1g-dev libssl-dev libgd-dev libgd-dev geoip-bin libgeoip-dev
```

如果你是升级就输入make，如果你是安装就输入make install

<div class="admonition shadow-sm admonition-primary"><div class="admonition-body">注意：如果已经安装nginx，输入make install会覆盖原来目录下的nginx</div></div>回车后没有意外的情况下你就可以看到Nginx 安装/升级 完毕

此时Nginx的程序路径应该是/usr/local/nginx/sbin/nginx （升级的话程序路径原封不动，把下文的Nginx程序路径改成你原先Nginx程序路径）

输入

```
/usr/local/nginx/sbin/nginx -t
```

回车，查看配置文件路径

一般为/usr/local/nginx/conf/nginx.conf，（升级的话就复制替换下文的相关路径即可）

输入

```
vim /usr/local/nginx/conf/nginx.conf
```

回车，编辑配置文件

如果是全新安装，在http块中添加以下内容

```
server {
 listen 443 quic reuseport;
 listen 443 ssl default_server;
 http2 on;
 http3 on;
 add_header Alt-Svc 'h3=":443"; h3-29=":443"; h3-Q050=":443"; ma=86400, h2=":443"; ma=3600'; #支持多种HTTP3版本
 ssl_certificate /root/card/example.com.pem; #替换成你的ssl证书公钥路径
 ssl_certificate_key /root/card/example.com.key; #替换成你的ssl证书私钥路径

 location / {
  proxy_pass https://10.0.0.1:444; #后端服务器地址，请替换成你的源站地址，设置成10.0.0.1:444是为了方便讲解，下文相关配置不在赘述
  proxy_set_header Host $host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  #下面是缓存相关配置，不用可以删除。注意，如果你想添加，则需要在http块里添加proxy_cache_path 你想设置的缓存路径 levels=1:2 keys_zone=my_cache:10m max_size=10g inactive=60m use_temp_path=off;
  proxy_cache my_cache;
  proxy_cache_key "$scheme$request_method$host$request_uri$http_accept_encoding";
  proxy_cache_valid 200 302 12h;
  proxy_cache_valid 404 1m;
  proxy_cache_background_update on;
  proxy_cache_use_stale error timeout invalid_header updating;
  proxy_http_version 1.1;
  proxy_set_header Connection "";
  keepalive_timeout 75s;
  keepalive_requests 1000;
 }
}
```

如果你已经拥有了相关配置，只需要支持http3，则只需要在server块中添加

```
listen 443 quic reuseport;
add_header Alt-Svc 'h3=":443"; h3-29=":443"; h3-Q050=":443"; ma=86400, h2=":443";
```

然后输入/usr/local/nginx/sbin/nginx 回车运行即可，你也可以把它加入到systemctl中

安装与配置WireGuard
--------------

在源站主机和跳板机都安装WireGuard

```
sudo<span style="background-color: #ffffff;"> </span><span class="token function" style="background-color: #ffffff;">apt</span><span style="background-color: #ffffff;"> </span><span class="token function" style="background-color: #ffffff;">install</span><span style="background-color: #ffffff;"> </span><span class="token parameter variable" style="background-color: #ffffff;">-y</span><span style="background-color: #ffffff;"> wireguard</span>
```

在任意安装了WireGuard的主机内任意准备一个空文件夹，在里面输入

```
wg genkey > server.key
```

回车，生成源站主机连接时所用的私钥文件

再输入

```
wg pubkey < server.key > server.key.pub
```

回车，生成源站主机连接时所用的公钥文件  
输入

```
wg genkey > clients.key
```

回车，生成跳板机主机连接时所用的私钥文件

再输入

```
wg pubkey < clients.key > clients.key.pub
```

回车，生成跳板机连接时所用的公钥文件

输入

```
cat server.key && cat server.key.pub && cat client.key && cat client.key.pub
```

显示所有公钥与私钥

在源站主机与跳板机输入以下的命令并按i键在/etc/wireguard/的文件夹内新建并编辑文件wg0.conf

```
vim /etc/wireguard/wg0.conf
```

然后在源站主机输入以下配置

```
[Interface]
PrivateKey = <源站主机的私钥>
Address = 10.0.0.1/24
ListenPort = 51820
MTU = 1300 #这个MTU这里设置的算是比较保守的了，大家可以根据自己的需求设定，不适宜太高，不然会出问题的。

[Peer] #你可以按照此格式添加更多跳板机，当然需要生成更多公钥与私钥
PublicKey = <跳板机的公钥> 
AllowedIPs = 10.0.0.2/32 
Endpoint = 跳板机的公网IP:51820 
PersistentKeepalive = 25
```

在跳板机输入以下配置

```
[Interface]
PrivateKey = <跳板机的私钥>
Address = 10.0.0.2/24
ListenPort = 51820
MTU = 1300 #与源站主机配置保持一致，下文不再赘述

[Peer]
PublicKey = <源站主机的公钥>
AllowedIPs = 10.0.0.1/32
```

然后两台主机都按Esc退出编辑模式再输入:wq回车退出并保存

两台主机再通过以下命令启动服务

```
<span class="token function" style="font-family: Consolas, Monaco, monospace;">sudo</span><span style="font-family: Consolas, Monaco, monospace;"> systemctl </span><span class="token builtin class-name" style="font-family: Consolas, Monaco, monospace;">enable</span> <span class="token parameter variable" style="font-family: Consolas, Monaco, monospace;">--now</span><span style="font-family: Consolas, Monaco, monospace;"> wg-quick@wg0</span>
```

这样WireGuard与Nginx就算是配置的差不多了，此时应该可以用HTTP3访问了

udp伪装tcp
--------

因为某些原因，大陆内与大陆外的主机使用WireGuard组网可能会导致运营商封端口，tcp相对比udp安全，所以将udp伪装成tcp比较有必要（但是仍然有几率）。目前比较主流的有两个方式，我分开讲

### 使用udp2raw伪装

从此下载：[udp2raw项目releases](https://github.com/wangyu-/udp2raw/releases)

把你的跳板机WireGuard配置成以下内容

```
[Interface]
PrivateKey = <跳板机的私钥>
Address = 10.0.0.2/24
ListenPort = 51820
MTU = 1300
PreUp = nohup <你的udp2raw绝对路径> -s -l0.0.0.0:4099 -r 127.0.0.1:51820 -k "<这里设置你自己的密码>" --raw-mode faketcp -a > /var/log/udp2raw.log &
PostDown = killall <你的udp2raw程序名> || true

[Peer]
PublicKey = <源站主机的公钥>
AllowedIPs = 10.0.0.1/32
```

把你的源站主机WireGuard配置成以下内容

```
[Interface]
PrivateKey = <源站主机的私钥>
Address = 10.0.0.1/24
ListenPort = 51820
MTU = 1300
PreUp = nohup <你的udp2raw程序绝对路径> -c -l0.0.0.0:3333  -r<你的跳板机公网ip>:4099  -k "<你设置的密码>" --raw-mode faketcp -a > /var/log/udp2raw.log &
#如果你有多个跳板机，可以添加多个上面的那一条配置，但是要保证不同的本地端口,日志路径,跳板机公网ip。
#如果你使用ipv6，则0.0.0.0替换为[::]，127.0.0.1替换为[::1]，其余ipv6地址记得用[]包裹
PostDown = killall <你的udp2raw程序名> || true

[Peer] 
PublicKey = <跳板机的公钥>
AllowedIPs = 10.0.0.2/32
Endpoint = 127.0.0.2:3333
PersistentKeepalive = 25
```

然后重启两端的WireGuard

```
sudo systemctl restart wg-quick@wg0
```

那么一切就大概率大功告成了，如果无法连接，你可以看看两端的/var/log/udp2raw.log文件  
如果你不喜欢udp2raw，那么还有更好的选择：phantun

### 使用Phantun伪装

phantun相比udp2raw，性能更高，MTU开销更少，也就是MTU能拉更高，代价就是使用变得更复杂了

从此下载：[phantun项目releases](https://github.com/dndx/phantun/releases)

确保你的跳板机与源站主机都安装了iptables

如果没有则用以下命令安装（默认自带ip6tables）

```
apt install iptables
```

在源站主机运行以下命令

```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
ip6tables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```

对出站流量进行掩饰，注意这里的eth0需要改成你的网卡名字，这里使用eth0是因为大多数人的网卡名称就是eth0

在跳板机运行以下命令

```
iptables -t nat -A PREROUTING -p tcp -i eth0 --dport 4567 -j DNAT --to-destination 192.168.201.2
#ipv4 DNAT
ip6tables -t nat -A PREROUTING -p tcp -i eth0 --dport 4567 -j DNAT --to-destination fcc9::2
#ipv6 DNAT
```

然后把你的源站主机WireGuard配置成以下内容

```
[Interface]
PrivateKey = <源站主机的私钥>
Address = 10.0.0.1/24
ListenPort = 51820
MTU = 1300
PreUp = RUST_LOG=info <你的phantun_client程序绝对路径> --local 127.0.0.1:3333 --remote 你的跳板机公网ip:56789 &> /var/log/phantun_client.log &
#再讲一遍，如果你有多个跳板机，可以添加多个上面的那一条配置，但是要保证不同的本地端口,日志路径,跳板机公网ip。
PostDown = killall phantun_client || true

[Peer] 
PublicKey = <跳板机的公钥>
AllowedIPs = 10.0.0.2/32
Endpoint = 127.0.0.1:3333
PersistentKeepalive = 25
```

然后把你的跳板机WireGuard配置成以下内容

```
[Interface]
PrivateKey = <跳板机的私钥>
Address = 10.0.0.2/24
ListenPort = 7777
MTU = 1300
PreUp = RUST_LOG=info <你的phantun_server程序绝对路径> --local 56789 --remote 127.0.0.1:7777 &> /var/log/phantun_server.log &
PostDown = killall phantun_server || true

[Peer] 
PublicKey = <源站主机的公钥>
AllowedIPs = 10.0.0.1/32
```

然后重启两端的WireGuard

```
sudo systemctl restart wg-quick@wg0
```

那么一切就大概率大功告成了，如果无法连接，你可以看看两端的/var/log/phantun\_server.log或/var/log/phantun\_client.log文件