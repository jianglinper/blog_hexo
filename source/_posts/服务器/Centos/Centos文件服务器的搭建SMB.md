---
title: Centos 文件服务器的搭建 Samba
categories:
  - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/4cdc7b8e2cc87d23528c26f89c77cede.png
main_color: '#D6D5D1'
tags:
  - Samba
  - Centos
  - 食用教程
abbrlink: d647
date: 2022-12-19 00:20:00
---



# Linux文件服务器的搭建

## 1. 关闭SELinux, 防火墙

   ~~~
   setenforce 0
   getenforce
   vim /etc/sysconfig/selinux #SELINUX=enforcing 改成 SELINUX=disable
   systemctl stop firewalld.service
   systemctl disable firewalld.service
   ~~~

   

## 2. 安装软件

   ~~~
   yum install -y samba samba-client
   ~~~

   

## 3. 编辑配置文件，共享/var/www/html/目录

~~~
mv /etc/samba/smb.conf /etc/samba/smb.conf.bak
vim /etc/samba/smb.conf
~~~

​		在这里插入

~~~
 [share]
    comment = It is a test
    path = /var/www/html
    browseable = yes
    writable = yes 
~~~

## 4. 创建共享用户

   ~~~
    useradd van
    smbpasswd -a van
    #输入 密码
    pdbedit -L #查看共享用户
   ~~~

   

## 5. 启动服务

   ~~~
   systemctl start smb # 启动服务
   systemctl enable smb # 开机自启
   
   ss -antp | grep smbd #查看是否运行
   ~~~

   

## 6. 设置目录的本地权限

   ~~~
   setfacl -m u:root:rwx /var/www/html
   
   setfacl -m u:(用户):rwx /(目录)
   ~~~

