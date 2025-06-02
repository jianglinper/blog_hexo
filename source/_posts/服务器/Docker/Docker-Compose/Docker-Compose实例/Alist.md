---
title: Docker-compose实例 Alist
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
  - Alist
abbrlink: '4966'
date: 2025-02-24 21:33:08
---


🗂️ 一个支持多种存储的文件列表程序，使用 Gin 和 Solidjs。

Alist文档：[https://alist.nn.ci/zh/](https://alist.nn.ci/zh/)

> AList 从一开始就设计为易于安装，并且可以在所有平台上使用。 多种存储 AList 支持多个存储提供商，包括本地存储、阿里云盘、OneDrive、Google Drive 等，且易于拓展。


配置文件中-数据库的配置

参考文档：[#database](https://alist.nn.ci/zh/config/configuration.html#database)

```json
"database": {
    "type": "mysql",
    "host": "mysql",
    "port": 3306,
    "user": "alist",
    "password": "alist",
    "name": "alist",
    "db_file": "data/data.db",
    "table_prefix": "x_",
    "ssl_mode": "",
    "dsn": ""
  }
```


docker-compose

```yaml
services:
  alist:
    image: 'xhofe/alist:latest'
    container_name: alist
    hostname: alist
    restart: always
    ports:
      - '5244:5244'
    volumes:
      - './data:/opt/alist/data'
    environment:
      - PUID=0
      - PGID=0
      - UMASK=022

# 个人配置
#    networks:
#      - net
#networks:
#  net:
#    external: true
#    name: lin-net
```

‍
