---
title: Centos 系统安装 Docker
categories:
  - 服务器
tags:
  - Centos
  - Docker
  - 食用教程
abbrlink: f4db
date: 2025-02-24 21:31:00
---

> 安装前必读
>
> 环境：Centos7.9
>
> Linux内核：3.10

## 查看当前内核版本

```bash
uname -r
```


## 使用 root 权限更新 yum 包

```bash
yum -y update
```

> **区分：**
>
> yum -y update：升级所有包同时也升级软件和系统内核；
>
> yum -y upgrade：只升级所有包，不升级软件和系统内核

## 卸载旧版本

```bash
yum remove docker  docker-common docker-selinux docker-engine
```

## 安装需要的软件包

**yum-util 提供yum-config-manager功能，另两个是devicemapper驱动依赖**

```bash
yum install -y yum-utils device-mapper-persistent-data lvm2
```

## 设置 yum 源

设置一个yum源，下面两个都可用

```bash
# 中央仓库
yum-config-manager --add-repo http://download.docker.com/linux/centos/docker-ce.repo

# 阿里仓库 推荐
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

## 选择docker版本并安装

### 查看可用版本

```bash
yum list docker-ce --showduplicates | sort -r
```

### 安装Docker

```bash
yum -y install docker-ce
```

**安装指定版本**

```bash
sudo yum -y install docker-ce-17.12.1.ce
```

