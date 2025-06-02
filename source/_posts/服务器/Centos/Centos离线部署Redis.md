---
title: Centos 离线部署 Redis
categories:
  - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/080ee82692de805283a33e3e1e1db7b4.png
main_color: '#A42122'
tags:
  - Kafka
  - Centos
  - 食用教程
abbrlink: bb70
date: 2024-10-15 00:21:00
---

Linux 系统如何离线部署 Redis（下载、安装、配置、启动、连接）

# 1、下载安装包和编译所需的 rpm 工具包

​    网上有许多下载资源的途径，这里我直接分享了一下我事先上传到网盘的资源，以供伙伴们下载，节省时间：

# 2、上传至服务器

进入rpm目录，安装rpm工具包

~~~shell
# 进入rpm目录
cd rpm目录路径
 
# 安装rpm工具包
rpm -ivh *.rpm --nodeps --force
~~~

# 3、随后就正常安装Redis

[Centos7 安装Redis](/p/20240715000900.html)
