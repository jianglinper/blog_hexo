---
title: Docker更换工作目录
categories:
  - 服务器
tags:
  - Docker
abbrlink: ded3
date: 2025-02-26 08:43:30
---

由于docker默认工作目录在`/var/lib/docker/`下，生产环境的系统磁盘有限，所以我们需要将默认目录修改到自定义的目录下，本篇修改到`/app/docker/`目录下

**注意：此操作可能会造成Docker数据丢失，建议在刚安装完docker后进行此操作**

原位置`/var/lib/docker/`

## 修改配置

```shell
mkdir /etc/docker
vim /etc/docker/daemon.json

# 在/etc/docker/daemon.json 文件内添加

{
  "data-root": "/app/docker"
}

mdkir /app/docker/

# 如果需要转移数据，此操作必须在转移数据后操作！！！
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 转移数据（可选）

如果原docker环境内有数据可将原目录拷贝到新目录

```shell

mv /var/lib/docker/* /app/docker/

...进行reload配置，重启docker
```

## 删除数据

转移后如果运行正常 可删除docker原始目录

```shell
rm -rf /var/lib/docker
```
