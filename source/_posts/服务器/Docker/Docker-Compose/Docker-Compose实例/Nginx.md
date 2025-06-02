---
title: Docker-compose实例 Nginx
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
  - Nginx
abbrlink: '3939'
date: 2025-02-24 21:33:08
---

> Nginx (engine x) 是一个高性能的HTTP和反向代理web服务器 ，同时也提供了IMAP/POP3/SMTP服务。Nginx是由伊戈尔·赛索耶夫为俄罗斯访问量第二的Rambler.ru站点（俄文：Рамблер）开发的，公开版本1.19.6发布于2020年12月15日。

[nginx](https://nginx.org/en/)

#### 需配置文件 （nginx.conf、default.conf）

```shell
链接
```

```yaml
version: '2.0'
services:
  nginx:
    image: nginx
    restart: always
    container_name: nginx
    ports:
      - '81:81'
    volumes:
      - '/volume1/docker/nginx/conf.d:/etc/nginx/conf.d'
      - '/volume1/docker/nginx/log:/var/log/nginx'
      - '/volume1/docker/nginx/conf/nginx.conf:/etc/nginx/nginx.conf'
      - '/volume1/docker/nginx/html:/usr/share/nginx/html'
      - '/volume1/docker/nginx/etc/letsencrypt:/etc/letsencrypt'
    networks:
      bridge0:
        ipv4_address: 172.20.0.11
networks:
  bridge0:
    external: true
    name: bridge0

```
