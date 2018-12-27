---
title: Deploy v2ray on bandwagon with docker
date: 2018-12-25 18:15:31
tags:
- docker
- v2ray
- vps
categories: Technology
lang: en
---

1. Pull v2ray official image
``` bash
docker pull v2ray/official
```
2. Prepare `config.json`. Then create `v2ray` folder under /etc, and copy `config.json` into it.
``` json
{
    "log": {
        "access": "/var/log/v2ray/access.log",
        "error": "/var/log/v2ray/error.log",
        "loglevel": "warning"
    },
    "inbound": {
        "port": 527,
        "protocol": "vmess",
        "settings": {
            "udp": true,
            "clients": [
                {
                    "id": "<your-uuid>",
                    "level": 1,
                    "alterId": 527
                }
            ]
        },
        "streamSettings": {
            "network": "kcp",
            "kcpSettings": {
                "mtu": 1350,
                "tti": 50,
                "uplinkCapacity": 100,
                "downlinkCapacity": 100,
                "congestion": false,
                "readBufferSize": 2,
                "writeBufferSize": 2,
                "header": {
                    "type": "wechat-video"
                }
            }
        }
    },
    "outbound": {
        "protocol": "freedom",
        "settings": {}
    },
    "inboundDetour": [
        {
            "protocol": "shadowsocks",
            "port": 9052,
            "settings": {
                "method": "chacha20-poly1305",
                "password": "<your-password>",
                "udp": true,
                "level": 1,
                "ota": false
            }
        }
    ],
    "outboundDetour": [
        {
            "protocol": "blackhole",
            "settings": {},
            "tag": "blocked"
        }
    ],
    "routing": {
        "strategy": "rules",
        "settings": {
            "rules": [
                {
                    "type": "field",
                    "ip": [
                        "0.0.0.0/8",
                        "10.0.0.0/8",
                        "100.64.0.0/10",
                        "127.0.0.0/8",
                        "169.254.0.0/16",
                        "172.16.0.0/12",
                        "192.0.0.0/24",
                        "192.0.2.0/24",
                        "192.168.0.0/16",
                        "198.18.0.0/15",
                        "198.51.100.0/24",
                        "203.0.113.0/24",
                        "::1/128",
                        "fc00::/7",
                        "fe80::/10"
                    ],
                    "outboundTag": "blocked"
                }
            ]
        }
    }
}
```
3. Deploy
``` bash
docker run -d --name v2ray -v /etc/v2ray:/etc/v2ray -p 527:527 -p 527:527/udp v2ray/official v2ray -config=/etc/v2ray/config.json
```
