---
title: Ubuntu安装MySQL8.1
categories:
  - 服务器
tags:
  - MySQL
  - Ubuntu
  - 食用教程
abbrlink: ea1d
date: 2024-12-30 08:56:31
---

## 1、确定Ubuntu版本

```bash
# 查看版本
lsb_release -a
```

```bash
# 例如我的版本：
root@huanfa:/app/mysql/data/mysql# lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 22.04.5 LTS
Release:        22.04
Codename:       jammy
```

## 2、在MySQL官网查看版本并下载

```bash
# 通过官网连接，按照下图下载安装包
https://downloads.mysql.com/archives/community/
```

```bash
# 在自己建的文件夹中下载文件
# 下载对应版本的文件（DEB Bundle）
sudo wget https://downloads.mysql.com/archives/get/p/23/file/mysql-server_8.1.0-1ubuntu22.04_amd64.deb-bundle.tar
```

## 3、解压文件

```bash
# 括号中的是指定路径，自选加不加（解压的文件不会自己创建文件夹，建议加一下，xxx表示对应文件夹）
sudo tar -xvf mysql-server_8.1.0-1ubuntu22.04_amd64.deb-bundle.tar （-C xxx）
```

## 4、安装MySQL

```bash
# 在安装过程中可能会遇到缺少libaio1、libmecab2包，通过以下方法执行
sudo apt-get update
sudo apt-get install libaio1
sudo apt-get install libmecab2

# libmecab2 在安装时会显示需要新建root密码
```

```bash
# 依次安装包。由于包之间有依赖关系，一定要按照顺序安装依次安装包。
# 由于包之间有依赖关系，一定要按照顺序安装

sudo dpkg -i mysql-common_8.1.0-1ubuntu22.04_amd64.deb
sudo dpkg -i mysql-community-client-plugins_8.1.0-1ubuntu22.04_amd64.deb
sudo dpkg -i libmysqlclient22_8.1.0-1ubuntu22.04_amd64.deb
sudo dpkg -i libmysqlclient-dev_8.1.0-1ubuntu22.04_amd64.deb
sudo dpkg -i mysql-community-client-core_8.1.0-1ubuntu22.04_amd64.deb
sudo dpkg -i mysql-community-client_8.1.0-1ubuntu22.04_amd64.deb
sudo dpkg -i mysql-client_8.1.0-1ubuntu22.04_amd64.deb
sudo dpkg -i mysql-community-server-core_8.1.0-1ubuntu22.04_amd64.deb
sudo dpkg -i mysql-community-server_8.1.0-1ubuntu22.04_amd64.deb
sudo dpkg -i mysql-server_8.1.0-1ubuntu22.04_amd64.deb
```

## 5、确认是否安装成功并新建外部可访问的root账号

### 1）确认安装成功

```bash
# 安装完成后验证MySQL是否安装成功
mysql -u root -p 
# 密码为自己新建的，如果没有新建就不需要填密码
```

```bash
# 正常安装完成后应该和下面相差不大
root@huanfa:/app/mysql/data/mysql# mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.1.0 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 8.1.0 for Linux on x86_64 (MySQL Community Server - GPL)

Connection id:          11
Current database:
Current user:           root@localhost
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         8.1.0 MySQL Community Server - GPL
Protocol version:       10
Connection:             Localhost via UNIX socket
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    utf8mb4
Conn.  characterset:    utf8mb4
UNIX socket:            /var/run/mysqld/mysqld.sock
Binary data as:         Hexadecimal
Uptime:                 19 min 0 sec

Threads: 3  Questions: 17  Slow queries: 0  Opens: 140  Flush tables: 3  Open tables: 59  Queries per second avg: 0.014
--------------

mysql> 
```

### 2）新建外部访问的root账号

```bash
# 安装完成后查看用户信息
select host,user from mysql.user;

# 创建用户，再给用户给权限
create user root@'%' identified with mysql_native_password by 'root';
grant all on *.* to root@'%' with grant option;
flush privileges;
exit;

# 'root'改为自己的密码

# 修改MySQL配置文件中的bind-address
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
# 在文件中修改或添加如下配置
bind-address    = 0.0.0.0
# 重启mysql
sudo service mysql restart
```

## 6、修改文件存储位置

```bash
# 停止MySQL服务
sudo service mysql stop
```

```bash
# 创建新的数据存储目录
sudo mkdir -p /data/mysql

# 复制MySQL的数据到新的目录中
sudo cp -ar /var/lib/mysql /data/mysql

# 修复新的存储目录的权限
sudo chown -R mysql:mysql /data/mysql

# 修改MySQL的配置文件
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
# 编辑上述文件，在文件中找到datadir项，=后面的值即为MySQL的存储路径，修改为新的路径即可。即：
datadir = /data/mysql

# 注意：只是修改MySQL的配置文件是不行的，因为Ubuntu有访问控制系统apparmor，所以还需要修改与该访问控制系统相关的文件
sudo vim /etc/apparmor.d/usr.sbin.mysqld
# 查找到以下内容
/var/lib/mysql/ r,
/var/lib/mysql/** rwk,
# 并将其修改为：
/data/mysql/ r,
/data/mysql/** rwk,
# 修改访问控制文件
sudo vim /etc/apparmor.d/abstractions/mysql
# 查到到以下内容
/var/lib/mysql{,d}/mysql{,d}.sock rw
# 并将其修改为：
/data/mysql{,d}/mysql{,d}.sock rw

# 重启apparmor服务
sudo service apparmor restart
# 重启MySQL服务
sudo service mysql start
```

```bash
# 在MySQL中验证存储位置
show variables like '%datadir%';

# 如下就是修改成功了
mysql> show variables like '%datadir%';
+---------------+------------------------+
| Variable_name | Value                  |
+---------------+------------------------+
| datadir       | /var/lib/mysql/        |
+---------------+------------------------+
1 row in set (0.01 sec)
```

‍
