---
title: "求助！Help！"
draft: true
---

网站会时不时地出现无法访问的问题

以下是网站的整体结构

![](https://emnasop.cn/wp-content/uploads/2025/08/0d40d05c4d2e8fd84bd1fbbc9c419eaf.png)

因为运营商经常封端口，所以使用udp2raw伪装，虽然phantun性能开销更好但是因为phantun没有合并包所以包数量比udp2raw高，容易被再次被封

源nginx与Frpc之间再添加ng反代是为了将proxy-protocol转化为x-forwarded-for，因为nginx的real\_ip\_header只支持一种获取真实ip的方式

其中以下是出现故障时的连接状态

![](https://emnasop.cn/wp-content/uploads/2025/08/54e8947e823d42edec6f8719ef248cbe.png)

出故障时wg与frp几乎同时断开

在与源站同ipv4（NAT）的windows主机在出故障时SSH连接反向代理服务器1也出现了
