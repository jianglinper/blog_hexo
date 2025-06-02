---
title: Docker-compose实例 Memos
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
  - Memos
abbrlink: 177d
date: 2025-02-24 21:33:08
---

Memos 是一项隐私优先的轻量级笔记服务。轻松捕捉并分享您的精彩想法。

官网：[https://www.usememos.com](https://www.usememos.com)

仓库地址：[https://github.com/usememos/memos](https://github.com/usememos/memos)


docker-compose

```yaml
services:
  memos:
    image: 'docker.olinl.com.cn/neosmemo/memos:stable'
    container_name: memos
    hostname: memos
    restart: always
    ports:
      - '5230:5230'
    volumes:
      - './data:/var/opt/memos'

# 个人配置
    networks:
      - net:
networks:
  net:
    external: true
    name: lin-net
```
