---
title: Centos 安装 JDK
categories:
  - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/dd72c2573b0edb7b9d5072f1a59cf7ea.png
main_color: '#2365C4'
tags:
  - Java
  - Centos
  - 食用教程
abbrlink: 5a47
date: 2022-03-15 00:05:00
---

# 下载Java

## 1. 首先确认安装了wget和vim

## 2. 创建一个数据文件夹 并进入

```
mkdir /data #在根目录创建一个data文件夹
cd /data #进入data文件夹
```

![image-20220413104452324](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007887.png)

## 3. 下载java

```
wget https://repo.huaweicloud.com/java/jdk/8u201-b09/jdk-8u201-linux-x64.tar.gz
```

==如果出现 -bash: wget: 未找到命令 请执行指令安装==

```
yum -y install wget
```

下载成功

![image-20220413104646266](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007875.png)

## 4. 解压下载的文件夹

```
tar -zxvf jdk-8u201-linux-x64.tar.gz
```

解压完成

![image-20220413104732709](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007467.png)

## 5. 重命名并移动到/usr/local/jdk1.8文件夹去

```
mv jdk1.8.0_201 /usr/local/jdk1.8/
```

![image-20220413104815979](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007452.png)

![image-20220413104836723](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007184.png)

# 配置环境

## 1. 进入到系统环境配置

```
vim /etc/profile
```

![image-20220413105029582](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007444.png)

到最下面 按i插入

```
# Java1.8
export JAVA_HOME=/usr/local/jdk1.8
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

![image-20220413105124776](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007696.png)

按ESC键 :wq保存并退出

## 2. 刷新配置

```
source /etc/profile
```

![image-20220413105223203](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007171.png)

## 3. 验证java版本信息

```
java -version
```

![image-20220413105256816](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007453.png)

**至此 java安装成功**
