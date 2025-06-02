---
title: Centos 指令大全
categories:
   - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/3c4e96f7f3de3c1948edcaf5c8a35e8a.png
main_color: '#D6D5D1'
tags:
  - 命令
  - Centos
abbrlink: e3de
date: 2025-06-01 00:19:00
---

# 修改主机名

~~~shell
# 查看主机名
hostname
# 修改主机名
hostnamectl set-hostname 主机名
~~~

# 配置免密登录

1. 在A客户端上生成公钥和私钥

   ~~~shell
    ssh-keygen -t rsa
   ~~~

2. 拷贝及配置方案

   ~~~shell
   ssh-copy-id -i ~/.ssh/id_rsa.pub 'root@要拷贝到的机器ip'
   ~~~

   

# scp传输文件

## 1、从服务器上下载文件

格式：scp 用户名@服务器地址:要下载的文件路径 保存文件的文件夹路径

例如：把 192.168.0.101 上的 /data/test.txt 的文件下载到 /home（本地目录）

~~~shell
scp root@192.168.0.101:/data/test.txt /home
~~~

## 2、上传本地文件到服务器

格式：scp 要上传的文件路径 用户名@服务器地址:服务器保存路径

例如：把本机 /home/test.txt 文件上传到 192.168.0.101 这台服务器上的 /data/ 目录中

~~~shell
scp /home/test.txt root@192.168.0.101:/data/
~~~

## 3、从服务器下载整个目录

格式：scp -r 用户名@服务器地址:要下载的服务器目录 保存下载的目录

例如：把 192.168.0.101 上的 /data 目录下载到 /home（本地目录）

~~~shell
scp -r root@192.168.0.101:/data  /home/
~~~

## 4、上传目录到服务器

格式：scp -r 要上传的目录 用户名@服务器地址:服务器的保存目录

例如：把 /home 目录上传到服务器的 /data/ 目录

~~~shell
scp -r /home root@192.168.0.101:/data/
~~~

# 安装RPM包

~~~shell
# 安装rpm工具包
rpm -ivh *.rpm --nodeps --force



# 卸载
rpm -e firefox
~~~

## 赋权

~~~shell
chmod -R 777 


chmod -R 777 /usr/local/mysql/*
~~~



# 安装

~~~
yum -y install  epel-release
~~~

# centos 查看端口占用

~~~shell
yum -y install lsof

lsof -i tcp:80
~~~

## 停用防火墙

~~~shell
systemctl stop firewalld.service
systemctl disable firewalld.service
~~~
