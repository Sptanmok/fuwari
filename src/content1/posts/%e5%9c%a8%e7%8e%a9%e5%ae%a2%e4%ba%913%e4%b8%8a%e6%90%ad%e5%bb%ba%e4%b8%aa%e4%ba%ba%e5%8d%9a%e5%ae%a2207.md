---
title: 在玩客云3上搭建个人博客
published: 2025-04-21
description: "原因 原本的免费虚拟主机访问太慢了，加上免费的Frp越来越多，低功耗的玩客云3似乎是一个不错的建站选择 所需材料 玩客云3主机（本文使用WS1608，1.3版本） 12V DC电源（1.5A或以上）  [&hellip;]"
tags: [教程, 服务器/端, 硬件, 软件]
categories:  教程 服务器/端 硬件 软件
---
<div class="admonition shadow-sm admonition-primary"><div class="admonition-body">本站已迁移至云，不再使用玩客云3</div></div>原因
--

原本的免费虚拟主机访问太慢了，加上免费的Frp越来越多，低功耗的玩客云3似乎是一个不错的建站选择

<div class="admonition shadow-sm admonition-primary"><div class="admonition-body">注意：在南方地区，夏天玩客云3主机玩客云SOC平均温度普遍高于45℃，可能导致经常性死机，建议去除外壳或者增加SOC散热鳍片(1cm * 1cm左右)</div></div><div class="admonition shadow-sm admonition-primary"><div class="admonition-body">注意：经过实测，使用玩客云3搭建博客会使网站TTFB&gt;1s，糟糕的后端性能直接影响了网站响应速度。在多个目标同时访问也出现了明显卡顿。如果家中有经常的意外因素，可能导致网站整体稳定性偏弱。所以如果有高性能高稳定的需求，建议使用云服务器或者面板服（也叫虚拟主机？）。如果你只是想搭建轻量级的博客网站，并且访问量不大，那么玩客云3还是很有性价比和自定义性的。</div></div>所需材料
----

1. 玩客云3主机（本文使用WS1608，1.3版本）
2. 12V DC电源（1.5A或以上）
3. 千兆网线（百兆也行）
4. 一根两头公的USB线（工具）
5. 镊子（能短接的都行）（工具）
6. 螺丝刀（用于撬开外边的壳和打开里面的螺丝）（工具）
7. 能正常用的Windows电脑（工具）

刷机
--

在电脑上下载安装USB Burning Tool软件，再下载[armbian-onecloud](https://github.com/hzyitc/armbian-onecloud/releases)的Armbian-unofficial\_25.05.0-trunk\_Onecloud\_bookworm\_current\_6.12.17.burn.img.xz文件并解压

用螺丝刀从SD卡槽处撬开外面的外壳（要用一点力），再用螺丝刀打开里面的外壳，用力抽出里面的主板（也可能只有我的这么紧）

查看SD卡槽处是否有V1.3选项，如果有则证明是1.3主板的版本，如果没有则是1.0的版本

<div class="wp-caption alignnone" style="width: 857px"><div class="fancybox-wrapper lazyload-container-unload" data-fancybox="post-images" href="https://img.picui.cn/free/2025/04/21/6806652183217.png">![a72c3586-13c9-4c01-9159-563fa0829e75.png](data:image/svg+xml;base64,PCEtLUFyZ29uTG9hZGluZy0tPgo8c3ZnIHdpZHRoPSIxIiBoZWlnaHQ9IjEiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgc3Ryb2tlPSIjZmZmZmZmMDAiPjxnPjwvZz4KPC9zdmc+ "a72c3586-13c9-4c01-9159-563fa0829e75.png")</div>这是1.3的版本短接方法，图片来源于网络，当时忘拍了

</div><div class="wp-caption alignnone" id="attachment_212" style="width: 404px"><div class="fancybox-wrapper lazyload-container-unload" data-fancybox="post-images" href="https://emnasop.cn/wp-content/uploads/2025/04/c28c2f38-d17d-40bd-87d1-632073a374f3-300x128.png">![](data:image/svg+xml;base64,PCEtLUFyZ29uTG9hZGluZy0tPgo8c3ZnIHdpZHRoPSIxIiBoZWlnaHQ9IjEiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgc3Ryb2tlPSIjZmZmZmZmMDAiPjxnPjwvZz4KPC9zdmc+)</div>1.0的短接方法，图片来源于网络，当时忘拍了

</div>主板先把USB连接到电脑和玩客云主板上（要连接玩客云主板上靠近网口的USB接口）

电脑打开USB Burning Tool软件，先短接（按照对应版本短接）再通电，通电了以后再松开短接，若成功了USB Burning Tool上会有提示，若失败了（既软件上没有提示）可以重新短接通电看看

USB Burning Tool提示连接成功后（可以停止短接了）点击“文件”-“导入烧录包”，选择刚才下载解压的文件，然后点击开始，如果提示错误可以点击刷新试试

如果USB Burning Tool提示成功，就可以断开设备连接，关闭软件，断电进行下一步操作了（你可以把它放回壳里）

网站搭建
----

注意玩客云3使用ARM32架构的Amlogic S805 SOC，常见搭建方法可能不适用

<span style="font-family: 'Lexend Deca';">连接网线和电源并通电，在电脑上打开cmd运行命令ipconfig，查看以太网（也有可能是别的）的默认网关，复制粘贴到浏览器上访问，查看设备品牌，</span>

在家中寻找对应品牌的光猫，路由器之类的，查看背面的用户名和密码，在浏览器上输入用户名和密码，点击登陆，在用户侧信息中的DHCP IPv4地址池分配信息中查看一个名称叫onecloud设备的IP地址，使用Shell工具进行连接（本文使用FinalShell)

用户名输入root，密码输入1234，后面会让你配置一些东西

配置完成后，输入apt install nginx并回车，输入y确定（默认安装1.22.1版本）

再输入apt install php并回车，输入y确定（默认安装8.2.28版本）

再输入apt install php-fpm并回车，输入y确定

再输入apt install php-gd并回车，输入y确定

输入apt install vim并回车，输入y确定

输入vim /etc/nginx/sites-available/default并回车

并按I键

把39-44行前面的#号去掉（按照默认配置文件，下面的图是我修改配置以后的），把第41改成下面这样（如果8.2不行可以改到其他版本）

```
         fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
```

把第33行改成下面这个（**`/index.php?$args`**是让nginx把非目录的URL交给index.php处理，而不是直接返回404）

```
         try_files $uri $uri/ /index.php?$args;
```

再按Esc键退出编辑，输入:wq并回车保存

输入service nginx restart并回车

输入apt install mariadb回车，输入y确定（默认安装10.11.11版本）

再输入mysql\_secure\_installation回车，配置mariadb数据库

把wordpress的文件都解压到目录/var/www/html，在浏览器输入主机ip，并配置wordpress，数据库配置就是刚才你配置mariadb数据库的

输入chmod -R 777 /var/www/html并回车，给文件夹内所有文件提权

### 配置SSL

随便找一个免费的SSL证书生成网站，下载pem和key文件，复制到小主机上的任意目录，输入vim /etc/nginx/sites-available/default 并按I键

在文件中添加以下配置

```
    listen 443 ssl default_server;
    listen [::]:443 ssl default_server;
    ssl_certificate 你的pem文件路径;
    ssl_certificate_key 你的key文件路径;

再按Esc键退出编辑，输入:wq并回车保存
```

<div class="wp-caption alignnone" id="attachment_211" style="width: 590px">[<div class="fancybox-wrapper lazyload-container-unload" data-fancybox="post-images" href="https://emnasop.cn/wp-content/uploads/2025/04/e5da22f0-4ddc-4a37-a949-e73cd2850aa3-e1745320437303.png">![这应是修改后的/etc/nginx/sites-available/default文件](data:image/svg+xml;base64,PCEtLUFyZ29uTG9hZGluZy0tPgo8c3ZnIHdpZHRoPSIxIiBoZWlnaHQ9IjEiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgc3Ryb2tlPSIjZmZmZmZmMDAiPjxnPjwvZz4KPC9zdmc+)</div>](https://emnasop.cn/wp-content/uploads/2025/04/e5da22f0-4ddc-4a37-a949-e73cd2850aa3-e1745320437303.png)为了防止一些呆瓜直接复制，以图片的形式进行展示。你的/etc/nginx/sites-available/default文件应该是上图所示的格式，请按照中文注释进行相应修改，注释（绿色字体）可以不用模仿

</div>输入service nginx restart并回车

网站就算配置的差不多了

Frp内网穿透
-------

使用任意的免费Frp（请自行搜索），在Web控制台中配置节点（选可建站的）并设置为https，将域名解析到对应CNAME（控制台上有写）(如果要支持ipv6，请在域名解析设置中设置为A记录，记录值为控制台中生成的配置文件中服务器的ipv4地址，再添加AAAA记录为Frps服务器信息的ipv6地址，需要frps端那有ipv6地址）

下载其arm版本（没有64），解压到小主机的任意目录

在控制台中生成配置文件，复制其内容，清空原来frpc.ini的内容，并把复制的内容粘贴上去，并保存

再在解压的目录中运行命令nohup ./frpc -c frpc.ini &amp;并回车

再按Ctrl+C，这样网站就配置的差不多了

### Proxy-protocol配置（可选，推荐）

进行以下操作以让nginx获取真实的ip

在配置文件的每一个配置末尾添加以下配置

```
proxy_protocol_version = v2
```

输入vim /etc/nginx/sites-available/default并回车

把listen 80;和listen 443 ssl default\_server;修改为listen 80 proxy\_protocol;和listen 443 ssl proxy\_protocol;

并在后面添加

```
    proxy_set_header X-Real-IP       $proxy_protocol_addr;
    proxy_set_header X-Forwarded-For $proxy_protocol_addr;
    real_ip_header X-Real-IP;
```

并按Esc输入:wq退出并保存

输入vim /etc/nginx/nginx.conf并回车，在http块里添加

```
    log_format proxy '$proxy_protocol_addr - $remote_user [$time_local] '
                 '"$request" $status $body_bytes_sent '
                 '"$http_referer" "$http_user_agent"';
    access_log /var/log/nginx/access.log proxy;
```

并按Esc输入:wq退出并保存

输入service nginx restart并回车

这样Proxy-protocol就算配置完成

[点击这里查看更进阶的玩法](https://emnasop.cn/2025/06/29/wireguard%e7%bd%91%e7%bb%9c%e9%9a%a7%e9%81%93nginx%e5%8f%8d%e5%90%91%e4%bb%a3%e7%90%86http3%e6%95%99%e7%a8%8b/)