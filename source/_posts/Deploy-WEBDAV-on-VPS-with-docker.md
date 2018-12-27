---
title: Deploy WEBDAV on VPS with docker
date: 2018-12-25 18:32:08
tags:
- webdav
- docker
- vps
categories: Technology
lang: en
---

Use this command to start a webdav server.
```
docker run --restart always -v /home/webdav:/var/lib/dav -e AUTH_TYPE=Digest -e USERNAME=tony -e PASSWORD=secret1234 --publish 8080:80 -d bytemark/webdav
```

Project on dockerhub: https://hub.docker.com/r/bytemark/webdav/

The data is actually store at `/home/webdav/data/`.
