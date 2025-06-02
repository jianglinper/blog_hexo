---
title: Centos 安装 MongoDB
categories:
  - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/a7fbeaff157759db56013b324b5f079d.png
main_color: '#39B98F'
tags:
  - MongoDB
  - Centos
  - 食用教程
abbrlink: e395
date: 2023-10-21 00:07:00
---

## 1. 创建仓库 

```shell
vim /etc/yum.repos.d/mongodb-org-3.4.repo
```

```shell
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

## 2. 安装

```shell
yum install -y mongodb-org
```

## 3. 修改配置文件

```shell
vim /etc/mongod.conf
```

把bindIP改成 0.0.0.0所有的机器都可以访问

![修改BingdIP](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008609.png)

## 4. 启动MogoDB

```shell
# 启动
systemctl start mongod.service
# 停止
systemctl stop mongod.service 
# 重启
systemctl restart mongod.service 
```

## 5. 链接mongodb

```shell
mongo 127.0.0.1:27017
```

默认将数据文件存储在 `/var/lib/mongo`目录

默认日志文件在 `/var/log/mongodb`中。

如果要修改,可以在 `/etc/mongod.conf` 配置中指定备用日志和数据文件目录

### 6. mongodb设置密码

**登录mogodb**

```shell
mongo 127.0.0.1:7316

use admin

db.createUser({ user: "admin", pwd: "password", roles: [{ role: "root", db: "admin" }] })
```

**修改配置**

```shell
vim /etc/mongod.conf

security:
   authorization: enabled
```

**重启服务**

```shell
systemctl restart mongod
```
