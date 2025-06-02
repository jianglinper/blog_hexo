---
title: Docker-compose实例 Homarr
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
  - Homarr
abbrlink: 3af
date: 2025-02-24 21:33:08
---

> 使用 Homarr 简化服务器的管理 - 一个时尚、现代的仪表板，让您的所有应用程序和服务触手可及。使用 Homarr，您可以在一个方便的位置访问和控制所有内容。Homarr 与您添加的应用程序无缝集成，为您提供有价值的信息并让您完全控制。安装轻而易举，Homarr 支持多种部署方法。

[Homarr documentation](https://homarr.dev/)

```yaml
version: "3.8"
services:
  homarr:
    image: ghcr.io/ajnart/homarr:latest
    container_name: homarr
    restart: unless-stopped
    environment:
      - PUID=1000
      - PGID=100
      - TZ=Asia/Shanghai
      - PASSWORD=123456
    volumes:
      - /volume1/docker/homarr/configs:/app/data/configs
      - /volume1/docker/homarr/icons:/app/public/icons
      - /volume1/docker/homarr/img:/app/public/img
    ports:
      - 80:7575
      - 6001:7575
    networks:
      bridge0:
        ipv4_address: 172.20.0.7
networks:
  bridge0:
    external: true
    name: bridge0
```
