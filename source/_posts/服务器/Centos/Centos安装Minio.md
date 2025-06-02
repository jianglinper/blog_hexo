---
title: Centos 安装 Minio
categories:
   - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/4ddd54a3a9b3ceef8e08ef4d4f24be84.png
main_color: '#BC3B5F'
tags:
  - Minio
  - Centos
  - 食用教程
abbrlink: d070
date: 2024-10-03 00:17:00
---

# 1. 准备安装目录和文件

> 安装目录：/opt/minio

1. 创建目录

   ~~~shell
   mkdir -p /opt/minio
   ~~~

2. 下载文件

   ~~~shell
   wget https://dl.min.io/server/minio/release/linux-amd64/minio
   ~~~

   

# 2. 安装

## 赋权

~~~shell
#提权
chmod +x minio
~~~

## 设置用户名，密码（临时）

~~~shell
#旧版使用 MINIO_ACCESS_KEY MINIO_SECRET_KEY，
#新版 配置用户名密码
export MINIO_ROOT_USER=minioadmin
export MINIO_ROOT_PASSWORD=yourpassword

#查看环境变量
echo $MINIO_ROOT_USER
echo $MINIO_ROOT_PASSWORD
~~~

## 创建存储目录及日志文件

~~~shell
#创建存储目录
mkdir -p  /opt/minio/data
#进入
cd /opt/minio
#创建日志文件
touch minio.log
~~~

# 3. 后台启动

## 进入执行文件目录

~~~shell
nohup /home/minio/minio server --address :9800 --console-address :9889 /home/minio/data >/home/minio/minio.log 2>&1 &
~~~

~~~shell
 #备注：

nohup：后台启动  
./minio server：启动命令   
--address :9800：指定API端口   
--console-address :9889：指定控制台端口  
/opt/minio/data：指定存储目录  
>/opt/minio/minio.log 2>&1 ：控制台日志重定向到/opt/minio/minio.log文件中  
&：后台运行
~~~

# 4. 注册成服务

~~~shell
#切换文件目录
cd /opt/minio/
#创建目录
mkdir conf
#创建配置文件
vim  conf/minio.conf
~~~

## 编辑文件minio.conf

~~~shell
#数据存放目录
MINIO_VOLUMES="/opt/minio/data"
#端口号设置
MINIO_OPTS="--console-address :9000 --address :9001"
#MINIO_ROOT_USER="minioadmin"
#MINIO_ROOT_PASSWORD="minioadmin"
#用户名
MINIO_ROOT_USER="minio"
#密码
MINIO_ROOT_PASSWORD="minioAa123"
~~~

## 创建服务minio.service

~~~shell
vim /usr/lib/systemd/system/minio.service 



#minio配置文件

[Unit]
Description=MinIO
Documentation=https://docs.min.io
Wants=network-online.target
After=network-online.target

#minio文件具体位置
AssertFileIsExecutable=/opt/minio/minio

[Service]
WorkingDirectory=/opt/minio/data

User=root
Group=root

#创建的配置文件 minio.conf
EnvironmentFile=/opt/minio/conf/minio.conf
#ExecStartPre=/bin/bash -c "[ -n \"${MINIO_VOLUMES}\" ] || echo \"Variable MINIO_VOLUMES not set in /usr/local/minio/conf/minio.conf\""
ExecStart=/opt/minio/minio server --console-address :9000 --address :9001

#$MINIO_OPTS $MINIO_VOLUMES

# Let systemd restart this service always
Restart=always

# Specifies the maximum file descriptor number that can be opened by this process
LimitNOFILE=65536

# Disable timeout logic and wait until process is stopped
TimeoutStopSec=infinity
SendSIGKILL=no

[Install]
WantedBy=multi-user.target
~~~

## 启动服务

~~~shell
#将服务设置为每次开机启动
systemctl enable minio.service
#重新加载某个服务的配置文件，如果新安装了一个服务，归属于 systemctl 管理，要是新服务的服务程序配置文件生效，需重新加载
systemctl daemon-reload
#启动服务
systemctl start minio
#停止服务
systemctl stop minio
#重启服务
systemctl restrat minio
#查看服务状态
systemctl status minio.service
~~~

