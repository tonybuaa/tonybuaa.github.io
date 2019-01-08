---
title: Enable IPv6 support for shadowsocks in docker
date: 2019-01-08 15:43:26
tags:
- vps
- docker
- shadowsocks
- ipv6
categories: Technology
---

Bandwagon的VPS是没有IPv6支持的，如果想访问IPv6地址，需要用tunnel。搭建方法参见这个文章：[给搬瓦工 KVM 版 VPS 配置 IPv6 支持（基于 Linux CentOS 7）](https://www.bandwagonhost.net/2144.html)。

这里要说的是如果让docker支持获取IPv6地址，并且打开shadowsocks对IPv6的支持。

# 为Docker分配IPv6地址段
这个方法来自[IPv6 with Docker](https://docs.docker.com/v17.09/engine/userguide/networking/default_network/ipv6/)。通过测试，只需要在vps中编辑`/etc/docker/daemon.json`，如果没有此文件就先创建。然后加入：

```json
{
    "ipv6": true,
    "fixed-cidr-v6": "2001:470:24:b55::/64"
}
```

其中`fixed-cidr-v6`来自tunnelbroker的`Routed IPv6 Prefixes`。
重启Docker后，可以在container里ping通`ipv6.google.com`。

# 运行shadowsocks时加入IPv6支持选项
我一直使用[mritd/shadowsocks](https://hub.docker.com/r/mritd/shadowsocks/)，现在为了支持IPv6，只需新加一些参数。完整的命令如下：
```
docker run -dt --name ssserver -p 9052:9052 -p 9052:9052/udp mritd/shadowsocks \
-s "-s 0.0.0.0 -s :: -p 9052 -m chacha20-ietf-poly1305 -k <yourpassword> --fast-open -u -6"
```
主要增加了：
|参数|说明|
 :-------: | :---------
 `-s ::` | 绑定全部IPv6地址           
 `-6`    | 优先使用IPv6地址解析主机名 

其他参数可以参考：[shadowsocks/shadowsocks-libev](https://github.com/shadowsocks/shadowsocks-libev#docker)。