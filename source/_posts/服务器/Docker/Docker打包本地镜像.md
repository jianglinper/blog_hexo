---
title: Docker 打包本地镜像
categories:
  - 服务器
tags:
  - Docker
abbrlink: e8
date: 2025-02-26 21:09:30
---

## 1. 执行如下命令找到被打包镜像的名字和版本号 (版本号=TAG)

```shell
docekr images 
```

## 2. docker打包镜像的两种方式 (选一种执行即可)

```shell
docker save 镜像名字:版本号 > /root/打包名字.tar
docker save -o /root/打包名字.tar 镜像名字:版本号
```

## 3. 将打包镜像分发到其它宿主机的 /root/ 目录下
## 4. 将打成 tar 包的镜像 load 出来

```shell
docker load < /root/打包名字.tar
```

## 5. 查看 load 出来的镜像ID

```shell
docekr images
```

## 6. 刚 load 出来的镜像其名字、版本号均为 none, 我们要通过 tag 命令赋予名字和版本号

```shell
docker tag 镜像ID 镜像名字:版本号
```
