---
title: Nginx反向代理Minio
categories:
  - 服务器
tags:
  - Minio
  - Nginx
abbrlink: a503
date: 2024-12-30 08:56:31
---

## 前言

在部署Minio时，直接暴露其服务可能带来安全风险。通过Nginx作为反向代理，可以有效增强安全性并优化访问控制。本文将简明介绍如何配置Nginx以代理Minio服务，确保您的数据存储解决方案既安全又高效。让我们快速了解这一重要设置。

## 配置

**API端口反向代理配置：**

```shell
# minio-api反向代理配置
server {
    server_name  localhost;
    listen 9000; # 外网端口
    location / {
		proxy_set_header Host $http_host;
		proxy_pass  http://192.168.1.10:9001/;# minio地址
   }
}

```

> 如果出现 页面上显示“ The request signature we calculated does not match the signature you provided. Check your key and signing method.”可以使用此配置文件解决
>
> 签名是放在header头当中的，所以一定要设置 `proxy_set_header`
