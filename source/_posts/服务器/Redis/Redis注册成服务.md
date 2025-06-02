---
title: 将Redis注册成服务
categories:
  - 服务器
tags:
  - Redis
abbrlink: b3cc
date: 2024-12-30 08:56:31
---

在[安装Redis](安装Redis.md)后，需要进行后台启动，需要使Redis服务开机自启

## 配置

```shell

# 后台启动

vim redis.conf

## :257 daemonize no 改成 daemonize yes

```

新建一个系统服务文件

```shell
vim /etc/systemd/system/redis.service
```

```service
[Unit]
Description=redis-server
After=network.target

[Service]
Type=forking
ExecStart=/app/redis/bin/redis-server /app/redis/redis.conf
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

```shell
# 重载
systemctl daemon-reload

# 操作redis服务
## 启动
systemctl start redis
## 停止
systemctl stop redis
## 重启
systemctl restart redis
## 查看状态
systemctl status redis

# 开机自启
systemctl enable redis

# 查看redis进程
ps -ef | grep redis
```
