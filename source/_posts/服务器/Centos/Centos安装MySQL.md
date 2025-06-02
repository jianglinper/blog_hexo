---
title: Centos 安装 MySQL
categories:
  - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/d162f7f8e227d3aab4ade0aaae4ff240.png
main_color: '#00618A'
tags:
  - MySQL
  - Centos
  - 食用教程
abbrlink: c5b7
date: 2024-08-15 00:08:00
---

# 准备安装包

## 1. 打开mysql的官网

[MySQL :: Download MySQL Community Server](https://downloads.mysql.com/archives/community/)

如下图

## 2. 选择对应的版本

CentOS 是基于红帽的，Select OS Version: 选择 linux 7，如下图

![image-20220413105809011](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008561.png)

## 3. 右键复制链接地址

```
https://downloads.mysql.com/archives/get/p/23/file/mysql-8.0.27-1.el7.x86_64.rpm-bundle.tar

# mysql 5.7 地址
https://downloads.mysql.com/archives/get/p/23/file/mysql-5.7.43-1.el7.x86_64.rpm-bundle.tar
```

![image-20220413110056904](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008946.png)

## 4. 回到linux

进入/data目录

```
cd /data
```

输入wget下载指令

```
wget https://downloads.mysql.com/archives/get/p/23/file/mysql-8.0.27-1.el7.x86_64.rpm-bundle.tar
```

正在下载

![image-20220413110332034](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008563.png)

下载完成

![image-20220413110352076](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008176.png)

# 安装MySQL

## 1. 解压压缩包

在我们刚才的mysql安装包目录，运行命令解压，解压结果如下图

```
tar -xvf mysql-8.0.27-1.el7.x86_64.rpm-bundle.tar
```

![image-20220413110520221](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008736.png)

## 2. 运行安装命令，依次安装

如下图

```
rpm -ivh mysql-community-common-8.0.27-1.el7.x86_64.rpm --nodeps --force
rpm -ivh mysql-community-libs-8.0.27-1.el7.x86_64.rpm --nodeps --force
rpm -ivh mysql-community-client-8.0.27-1.el7.x86_64.rpm --nodeps --force
rpm -ivh mysql-community-server-8.0.27-1.el7.x86_64.rpm --nodeps --force
```

![image-20220413110958642](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008157.png)

## 3. 查看mysqld

安装完成后，运行命令 查看mysqld

如下图

```
rpm -qa | grep mysql
```

![image-20220413111040143](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008534.png)

## 4. 初始化mysql，并开启服务

```
mysqld --initialize;
chown mysql:mysql /var/lib/mysql -R;
systemctl start mysqld.service;
systemctl enable mysqld;
```

## 5.  查找mysql的初始密码

```
cat /var/log/mysqld.log | grep password
```

![image-20220413111429178](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008807.png)

## 6. 登陆MySQL

```
mysql -uroot -p
```

![image-20220413111533921](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008684.png)

## 7. 修改密码

虽然mysql安装完成，但是随便运行一个命令就会发现mysql强制要求我们改密码

![image-20220413111604454](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008587.png)

**运行命令 这样我们在本机用root用户登陆时的密码就改为了 root**

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
```

![image-20220413111638854](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008699.png)

**重新登录，再运行命令就正常了**

![image-20220413111717187](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008172.png)

# ？外部访问不到

端口问题需要开放端口

## 怎么开启一个端口

### 1. 添加

```
firewall-cmd --zone=public --add-port=3306/tcp --permanent
（--permanent永久生效，没有此参数重启后失效 3306是mysql的默认访问端口）
```

### 2. 添加端口外部访问权限

```
firewall-cmd --add-port=3306/tcp
```

### 3. 重新载入

添加端口后重新载入才能起作用

```
firewall-cmd --reload
```

这些之后，端口是开启成功的，如果没有成功，重启系统试试。

![image-20220413112047662](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008916.png)

# ？MySQL只允许本机访问

虚拟机上安装的mysql，发现用本地电脑的navicat链接不上。通过了解知道了原因，此写了一篇，省的以后自己在碰到。

错误如图。

![image-20220413113315870](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008585.png)

## 1. 登录到mysql中

```
mysql -u root -p 
```

![image-20220413113438646](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008073.png)

## 2. 选择mysql表

```
use mysql;
```

![image-20220413113446218](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008190.png)

## 3. 查询账号信息

```
select host from user where user='root';
```

![image-20220413113523941](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008391.png)

## 4. 修改帐号不允许从远程登陆

只能在localhost。这个时候只要在localhost的那台电脑，登入[MySQL](http://lib.csdn.net/base/14)后，更改 "mysql" 数据库里的 "user" 表里的 "host" 项，从"localhost"改称"%"

```
update user set host = '%' where user ='root';
```

![image-20220413113550750](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008584.png)

## 5. 刷新配置

```
flush privileges;
```

![image-20220413113616546](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008111.png)

## 6. 成功修改

![image-20220413113705717](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008770.png)
