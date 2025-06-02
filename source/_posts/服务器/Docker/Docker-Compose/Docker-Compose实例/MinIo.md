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

Minio 是一个基于Apache License v2.0开源协议的对象存储服务，虽然轻量，却拥有着不错的性能。它兼容亚马逊S3云存储服务接口，非常适合于存储大容量非结构化的数据。

官网：[https://www.minio.org.cn](https://www.minio.org.cn)

部署参考：[布署单节点单磁盘的MinIO服务](https://www.minio.org.cn/docs/minio/container/operations/install-deploy-manage/deploy-minio-single-node-single-drive.html#id15)​



docker-compose

```yaml
services:
  minio:
    image: 'docker.olinl.com.cn/minio/minio:latest'
    container_name: minio
    hostname: minio
    ports:
      - 9000:9000
      - 9001:9001
    volumes:
      - ./data:/data
    environment:
		# 用户名与密码
      - MINIO_ROOT_USER=admin
      - MINIO_ROOT_PASSWORD=Aa123456
    restart: always
    command: server --console-address ':9000' --address ':9001' /data  

# 个人配置
    networks:
      - net
networks:
  net:
    external: true
    name: lin-net
```

‍
