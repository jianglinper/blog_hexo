---
title: Cenots 安装 Docker
categories:
  - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/e6f8966c0d08372281a024e086afb580.png
main_color: '#2789EB'
tags:
  - Docker
  - Centos
  - 食用教程
abbrlink: 984c
date: 2024-10-15 00:01:00
---


> 安装前必读
> 在安装 Docker 之前，先说一下配置，我这里是Centos7
> Linux 内核：官方建议 3.10 以上，3.8以上貌似也可。

注意：本文的命令使用的是 root 用户登录执行，不是 root 的话所有命令前面要加 sudo

## 1. 查看当前的内核版本 

```shell
uname -r
```

![查看当前的内核版本](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072005073.png)

我这里是3.10 ，满足条件。

## 2. 使用 root 权限更新 yum 包

**生产环境中此步操作需慎重，看自己情况，学习的话随便搞**

```shell
yum -y update
```

> yum -y update：升级所有包同时也升级软件和系统内核；
>
> yum -y upgrade：只升级所有包，不升级软件和系统内核

## 3.卸载旧版本

```shell
yum remove docker  docker-common docker-selinux docker-engine
```

## 4.安装需要的软件包

**yum-util 提供yum-config-manager功能，另两个是devicemapper驱动依赖**

```shell
yum install -y yum-utils device-mapper-persistent-data lvm2
```

## 5.设置 yum 源

设置一个yum源，下面两个都可用

```shell
# 中央仓库
yum-config-manager --add-repo http://download.docker.com/linux/centos/docker-ce.repo

# 阿里仓库 推荐
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

## 6. 选择docker版本并安装

### 查看可用版本

```shell
yum list docker-ce --showduplicates | sort -r
```

![查看可用版本](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072005280.png)

### 安装Docker

```shell
yum -y install docker-ce
```

**安装指定版本**

~~~shell
sudo yum -y install docker-ce-17.12.1.ce
~~~



出现下图说明安装成功

![出现下图说明安装成功](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006405.png)

## 7. 启动 Docker 并设置开机自启

```shell
systemctl start docker
systemctl enable docker
```

### 常用Docker命令

```shell
# 重启Docker服务
systemctl restart docker

# 开启Docker服务
systemctl start docker

# 停止Docker服务
systemctl stop docker

# Docker服务开机自启
systemctl enable docker
```

## 8. Docker换源

### 创建文件

```shell
vim /etc/docker/daemon.json
```

### 添加镜像地址，写入下面内容

```json
{
    "registry-mirrors": [
        "https://hub-mirror.c.163.com/",
        "https://docker.mirrors.ustc.edu.cn/"
    ]
}
```

### 重启docker以及daemon

```shell
systemctl daemon-reload
systemctl restart docker
```

> **其他源介绍：**
>
> 科大镜像：https://docker.mirrors.ustc.edu.cn
> 网易：https://hub-mirror.c.163.com
> 七牛云加速器：https://reg-mirror.qiniu.com
> Docker中国区官方镜像：https://registry.docker-cn.com
> 阿里云：https://阿里云镜像服务id.mirror.aliyuncs.com
>
> 阿里云Docker加速服务 https://cr.console.aliyun.com





## 9. 一键安装docker（指定版本）

~~~shell
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
  
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
sudo yum makecache fast

sudo yum -y install docker-ce-17.12.1.ce



 sudo chmod +x /usr/local/bin/docker-compose
~~~



## 10、修改docker文件夹位置

原文件位置：`/var/lib/docker/`

更改到：`/app/docker`

~~~shell
mkdir /etc/docker
vim /etc/docker/daemon.json

{
  "data-root": "/app/docker"
}
~~~

