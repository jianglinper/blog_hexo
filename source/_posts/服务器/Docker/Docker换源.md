---
title: Docker 换源
categories:
  - 服务器
tags:
  - Docker
  - 食用教程
abbrlink: f95c
date: 2024-12-24 21:09:30
---

> 因为中国区访问Docker出现问题，所以利用cloudflare搭建了一个Docker镜像源

## Docker换源

### 创建文件

```shell
vim /etc/docker/daemon.json
```

### 添加镜像地址，写入下面内容

```json
{
    "registry-mirrors": ["https://docker.olinl.com.cn"]
}
```

### 重启docker以及daemon

```shell
systemctl daemon-reload
systemctl restart docker
```

## 一键换源脚本

**请确保Docker刚安装，无其他配置，此操作会替换Docker配置文件**

```shell
mkdir -p /etc/docker
tee /etc/docker/daemon.json <<EOF
{
    "registry-mirrors": ["https://docker.olinl.com.cn"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
