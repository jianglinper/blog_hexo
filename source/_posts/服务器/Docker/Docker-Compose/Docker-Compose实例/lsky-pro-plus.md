---
title: Docker-compose实例 lsky-pro-plus
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
  - lsky-pro-plus
abbrlink: '1917'
date: 2025-02-24 21:33:08
---


docker-compose

```yaml
services:
  app:
    build:
      context: ..
      dockerfile: ./docker/Dockerfile
    image: lsky-pro-plus
    container_name: lsky-pro-plus
    environment:
      APP_NAME: "拾柒图床"
      APP_URL: "https://img.olinl.com"
      APP_LICENSE_KEY: "vrfhtbul1u3k"
      ADMIN_USERNAME: "jianglin"
      ADMIN_EMAIL: "jianglinper@163.com"
      ADMIN_PASSWORD: "Lin203428"
      DB_HOST: "mysql"
      DB_PORT: "3306"
      DB_DATABASE: "lsky"
      DB_USERNAME: "root"
      DB_PASSWORD: "root"
    ports:
      - '18002:80'
    volumes:
      - './data/app-code:/var/www'
      - './data/lsky-data:/var/www/data'
    restart: unless-stopped
    
```
