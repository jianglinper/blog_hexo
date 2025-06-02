---
title: 安装Nacos配置中心
categories:
  - 服务器
tags:
  - Nacos
abbrlink: '6975'
date: 2025-05-30 08:56:31
---

# 安装Nacos配置中心

## 一、安装Nacos

> 安装位置：/opt/nacos/

## 解压缩安装包到指定位置

~~~shell
tar -zxvf nacos-server-1.3.1.tar.gz 

- bin  			启动nacos服务的脚本目录
- conf 			nacos的配置文件目录
- target 		nacos的启动依赖存放目录
- data		  nacos启动成功后保存数据的目录
~~~

## 启动服务器

打开终端进入nacos的bin目录执行如下命令

~~~shell
./startup.sh -m standalone
或者
sh startup.sh -m standalone
~~~

## 访问nacos的web服务管理界面

- http://ip:8848/nacos/
- 用户名 和 密码都是nacos

nacos的默认端口号是8848，如果是阿里云服务器的话，记得在安全组开启端口

# 二、持久化到Mysql数据库

注意： Nacos持久化默认无法使用mysql8，如果按照正常方式配置，启动会报错 No DataSource Set

在0.7版本之前，在单机模式时nacos使用嵌入式数据库实现数据的存储，不方便观察数据存储的基本情况。0.7版本增加了支持mysql数据源能力，具体的操作步骤：

- 1.安装数据库，版本要求：5.6.5+
- 2.初始化mysql数据库，数据库初始化文件：nacos-mysql.sql
- 3.修改conf/application.properties文件，增加支持mysql数据源配置（目前只支持mysql），添加mysql数据源的url、用户名和密码。

~~~properties
spring.datasource.platform=mysql

db.num=1
db.url.0=jdbc:mysql://11.162.196.16:3306/nacos_devtest?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
db.user=nacos_devtest
db.password=youdontknow
~~~

参考文档：[Nacos安装 并且配置持久化到Mysql数据库](https://blog.csdn.net/su2231595742/article/details/122827205)
