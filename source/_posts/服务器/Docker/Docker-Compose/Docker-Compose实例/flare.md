---
title: Docker-compose实例 flare
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
  - flare
abbrlink: '1012'
date: 2025-02-24 21:33:08
---

一个十分轻量级的个人导航页

地址：[soulteary/docker-flare](https://github.com/soulteary/docker-flare)

作者博客：[从零开始搭建个人书签导航应用：Flare](https://soulteary.com/2022/02/23/building-a-personal-bookmark-navigation-app-from-scratch-flare.html)


docker-compose

```yaml
services:
  flare:
    image: 'docker.olinl.com.cn/soulteary/flare'
    container_name: flare
    hostname: flare
    restart: always
    # 启用账号登录模式
    command: flare --nologin=0
    environment:
      # 如需开启用户登录模式，需要先设置 `nologin` 启动参数为 `0`
      # 如开启 `nologin`，未设置 FLARE_USER，则默认用户为 `flare`
      - FLARE_USER=admin
      # 指定你自己的账号密码，如未设置 `FLARE_USER`，则会默认生成密码并展示在应用启动日志中
      - FLARE_PASS=admin
      # 是否开启“使用向导”，访问 `/guide`
      # - FLARE_GUIDE=1
    ports:
      - 80:5005
    volumes:
      - ./app:/app

# 个人配置
    networks:
     - lin-net
networks:
  lin-net:
    external: true
    name: lin-net
```
