---
title: docker-compose 网络深究
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
abbrlink: 8fa6
date: 2025-02-26 21:31:00
---

## 创建容器内的网络

```yaml
services:
  app:
    networks:
      - net

networks:
  net:
    driver: bridge
    name: app-network
```

## 绑定容器外的网络

**这个网络必须存在**

```yaml
services:
  app:
    networks:
      - net

networks:
  net:
    external: true
    name: lin-net
```

## 绑定ip地址

```yaml
services:
  app:
    networks:
      net:
        ipv4_address: 172.20.0.102
```
