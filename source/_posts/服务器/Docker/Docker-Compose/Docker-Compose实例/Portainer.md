---
title: Docker-compose实例 Portainer
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
  - Portainer
abbrlink: 5a3f
date: 2025-02-24 21:33:08
---

> Portainer是一个容器管理工具，提供图形化界面来方便地管理和控制Docker。通过拉取镜像、启动容器，映射端口和数据卷，可以轻松实现对容器的监控、日志查看、启动和停止等操作。首次登录Portainer需要创建管理员账户，并可以选择本地Docker进行管理。如果遇到端口冲突或容器名重复问题，需相应解决，如更改端口或删除重复容器。

[Kubernetes and Docker Container Management Software](https://www.portainer.io/)

```yaml
version: '3.1'
services:
  portainer:
    image: 6053537/portainer-ce
    container_name: portainer
    hostname: portainer
    restart: unless-stopped
    ports:
      - '6004:9000'
    volumes:
      - '/var/run/docker.sock:/var/run/docker.sock'
      - '/volume1/docker/portainer/data:/data'
    networks:
      bridge0:
        ipv4_address: 172.20.0.6
networks:
  bridge0:
    external: true
    name: bridge0

```
