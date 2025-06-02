---
title: Docker-compose实例 Halo
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
  - Halo
abbrlink: '2006'
date: 2025-02-24 21:33:08
---

> Halo 作为一款好用又强大的开源建站工具，配合上不同的模板与插件，可以很好地帮助你构建你心中的理想站点。它可以是你公司的官方网站，可以是你的个人博客，也可以是团队共享的知识库，甚至可以是一个论坛、一个商城。

[Halo - 强大易用的开源建站工具](https://www.halo.run/)

```yaml
version: '3'
services:
  halo:
    image: 'halohub/halo:2.11'
    container_name: halo
    hostname: alist
    restart: 'on-failure:3'
    volumes:
      - '/volume1/docker/halo/data:/root/.halo2'
    ports:
      - '6003:8090'
    healthcheck:
      test:
        - CMD
        - curl
        - '-f'
        - 'http://localhost:8090/actuator/health/readiness'
      interval: 30s
      timeout: 5s
      retries: 5
      start_period: 30s
    command:
      - '--spring.r2dbc.url=r2dbc:pool:mysql://mysql:3306/halo'
      - '--spring.r2dbc.username=root'
      - '--spring.r2dbc.password=root'
      - '--spring.sql.init.platform=mysql'
      - '--halo.external-url=http://10.0.0.10:6002/'
    networks:
      bridge0:
        ipv4_address: 172.20.0.9
networks:
  bridge0:
    external: true
    name: bridge0
```
