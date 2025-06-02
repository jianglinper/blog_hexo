---
title: Docker-compose实例 synctv
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
  - synctv
abbrlink: ac41
date: 2025-02-24 21:33:08
---
# synctv-Docker-Compose

[synctv/README-CN.md at main · synctv-org/synctv · GitHub](https://github.com/synctv-org/synctv/blob/main/README-CN.md)

> SyncTV 是一个允许您远程一起观看电影和直播的程序。它提供了同步观影、直播、聊天等功能。使用 SyncTV，您可以与朋友和家人一起观看视频和直播，无论他们在哪里。
>
> SyncTV 的同步观影功能确保所有观看视频的人都在同一点上。这意味着您可以暂停、快进快退、倍速等操作，其他人也会同步到同一点。

默认账号 root

默认密码 root

```yaml
version: '3.3'
services:
  synctv:
    container_name: synctv
    ports:
      - '6008:8080'
    environment:
      - PUID=0
      - PGID=0
      - TZ=Asia/Shanghai
    restart: always
    volumes:
      - '/volume1/docker/synctv/data:/root/.synctv'
    image: synctvorg/synctv
    networks:
      bridge0:
        ipv4_address: 172.20.0.12
networks:
  bridge0:
    external: true
    name: bridge0

```
