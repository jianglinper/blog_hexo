---
title: Cenots 安装 Docker
categories:
  - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/20fd18c74b08fa0d19f74c7b9b069401.png
main_color: '#F13C6F'
tags:
  - Maven
  - Centos
  - 食用教程
abbrlink: 984c
date: 2023-10-21 00:04:00
---

# 一、下载Maven

## 1. 下载对应的Maven

```
这里的版本是 apache-maven-3.8.1-bin.tar.gz
```

官网下载：https://maven.apache.org/download.cgi

以前版本下载地址：https://archive.apache.org/dist/maven/maven-3/

![image-20220413135540316](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007992.png)

## 2. 进入data目录

```
cd /data
```

## 3. 利用wget下载

如果提示这个 就在后面加上**--no-check-certificate**

![image-20220413135730057](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007553.png)

```
wget https://dlcdn.apache.org/maven/maven-3/3.8.5/binaries/apache-maven-3.8.5-bin.tar.gz --no-check-certificate
```

## 4. 解压

```
~~~
tar -zxvf apache-maven-3.8.5-bin.tar.gz
~~~
```

## 5. 重命名到/usr/local/maven文件夹

```
mv apache-maven-3.8.5 /usr/local/maven/
```

```
下载成功
```


![image-20220413135838014](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007259.png)

# 二、配置环境变量

## 1. 进入 /etc/profile

```
vim /etc/profile
```

## 2. 编辑

在末尾追加以下信息

```
# maven
export MAVEN_HOME=/usr/local/maven
export PATH=.:$PATH:$MAVEN_HOME/bin
```

## 3. 编译 profile 文件 (全局变量文件)

```
source /etc/profile
```

# 三、修改配置

## 1. 创建本地仓库目录 maven_repository

这里创建在 /usr/local/maven/目录下 (创建在任何位置都可以)

```
mkdir /usr/local/maven/maven_repository
```

## 2. 编辑

在文件末尾追加以下信息

```
# maven
export MAVEN_HOME=/usr/local/maven
export PATH=.:$PATH:$MAVEN_HOME/bin
```

## 3. 编译 profile 文件 (全局变量文件)

```
source /etc/profile
```

# 四、验证安装

## 1. 显示Maven版本号

成功显示版本信息，即代表安装完成

```
mvn -version
```
