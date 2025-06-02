---
title: Docker-compose实例 lucky
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
  - lucky
abbrlink: d426
date: 2025-02-24 21:33:08
---

一个小工具

官网：[https://lucky666.cn](https://lucky666.cn/)

GitHub：[https://github.com/gdy666/lucky](https://github.com/gdy666/lucky)


安装

参考：[Docker-Compose](https://lucky666.cn/docs/install#docker%E9%95%9C%E5%83%8F)


docker-compose

```yaml
services:
  lucky:
    image: 'gdy666/lucky'
    container_name: lucky
    hostname: lucky
    ports:
      - '8443:8443'
    volumes:
    - ./data:/goodluck
    - ./web:/web # 自定义页面
    restart: always

# 个人配置

#    networks:
#      net:
#        ipv4_address: 172.20.0.4
#networks:
#  net:
#    external: true
#    name: lin-net
```
