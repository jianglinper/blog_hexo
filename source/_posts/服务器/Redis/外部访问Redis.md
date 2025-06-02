---
title: 外部访问Redis
categories:
  - 服务器
tags:
  - Redis
abbrlink: ab90
date: 2024-12-30 08:56:31
---

[安装Redis](安装Redis.md)后，需要在外部进行访问

PS：生产环境请勿任意将Redis服务暴漏出来

## 修改配置

```shell
# 修改配置文件
vim redis.conf

## :75 将bind 172.0.0.1 -::1 改成 bind 0.0.0.0
```

设置完成
