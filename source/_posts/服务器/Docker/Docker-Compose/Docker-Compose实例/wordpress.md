---
title: Docker-compose实例 wordpress
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
  - wordpress
abbrlink: bec6
date: 2025-02-24 21:33:08
---

Wordpress是一个博客程序


docker-compose

```yaml
services:
  redis:
    image: 'docker.olinl.com.cn/wordpress:6.6.0'
    container_name: wordpress
    hostname: wordpress
    restart: always
    #ports:
    #  - '80:80'
    volumes:
      - ./data:/var/www/html
      - ./conf/uploads.ini:/usr/local/etc/php/conf.d/uploads.ini
    environment: # 数据库链接信息
      WORDPRESS_DB_HOST: mysql:3306
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_PASSWORD: root
      WORDPRESS_DB_USER: root
      WORDPRESS_DEBUG: 0
# 个人配置
    networks:
      - net
networks:
  net:
    external: true
    name: lin-net
```

‍

uploads.ini

```yaml
file_uploads = On
memory_limit = 256M
upload_max_filesize = 60M
post_max_size = 50M
max_execution_time = 360


extension=ixed.8.2.lin
```

‍
