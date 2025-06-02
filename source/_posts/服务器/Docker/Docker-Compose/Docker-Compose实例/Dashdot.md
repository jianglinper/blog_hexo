---
title: Docker-compose实例 Dashdot
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
  - Dashdot
abbrlink: 7fcd
date: 2025-02-24 21:33:08
---

[github.com](https://github.com/MauriceNino/dashdot)

> Dashdot中文名字“破折号”，它是一款简单、实用的开源现代服务器仪表盘，主要应用于小型 VPS 和私人服务器（比如说NAS），是一个界面非常漂亮的监控服务器面板！

```yaml
version: '3.1'
services:
  dashdot:
    stdin_open: true
    tty: true
    restart: unless-stopped
    privileged: true
    container_name: dashdot
    ports:
      - '6005:3001'
    volumes:
      - '/:/mnt/host:ro'
    environment:
      - DASHDOT_ENABLE_CPU_TEMPS=true
      - DASHDOT_OVERRIDE_OS=DS1522+
      - DASHDOT_ALWAYS_SHOW_PERCENTAGES=true
    image: 'mauricenino/dashdot:latest'
    networks:
      bridge0:
        ipv4_address: 172.20.0.10
networks:
  bridge0:
    external: true
    name: bridge0

```
