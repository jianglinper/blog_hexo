---
title: Centos 系统优化
categories:
  - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/f1e2c59c7ab44ca8e0f571118122e40f.png
main_color: '#24AF7A'
tags:
  - Centos
abbrlink: '8018'
date: 2025-03-01 00:14:00
---

> 食用前提 这是博主七零八落捡的一些 不知道有没有用 总之照着做就好了
> PS：此操作建议在安装完成之后立刻进行，适合纯净环境的服务器，请勿在正在运行生产环境的服务器上进行操作！！！

# 换源

> CentOS默认的yum源有时候不是国内镜像，导致yum在线安装及更新速度不是很理想，这时候需要将yum源设置为国内镜像站点（网易or阿里云等）。

```shell
# 备份yum源
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

#下载国内yum源配置文件
# 阿里源（推荐）：
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
# 网易源：
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo

# 清理yum缓存，并生成新的缓存
yum clean all
yum makecache

# 更新源
yum update
```

由于有些机器wget也没有下载，所以这里准备了阿里云的源，可直接使用

```shell
vi /etc/yum.repos.d/CentOS-Base.repo
```

```repo

# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the 
# remarked out baseurl= line instead.
#
#
 
[base]
name=CentOS-$releasever - Base - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/os/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/os/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7
 
#released updates 
[updates]
name=CentOS-$releasever - Updates - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/updates/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/updates/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7
 
#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/extras/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/extras/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7
 
#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/centosplus/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/centosplus/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7
 
#contrib - packages by Centos Users
[contrib]
name=CentOS-$releasever - Contrib - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/contrib/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/contrib/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/contrib/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

```

# 跳过图形化界面的用户添加

在CentOS上，图形化登录（如GNOME）通常要求您创建一个用户来登录。如果您不想创建新用户并希望直接使用现有用户登录，可以进行以下设置

```shell
# 编辑GDM配置文件
sudo vim /etc/gdm/custom.conf

## 在文件中找到 [daemon] 部分，并添加以下行：
AutomaticLoginEnable=True AutomaticLogin=<您的用户名>
例如：
AutomaticLogin=root

# 删除引导 并重启图形化环境
yum remove gnome-initial-setup.x86_64
init 3
init 5

# 重启系统
reboot
```

# 安装必要的软件包

在安装CentOS 7后，我们需要安装一些必要的软件包，以便更好地进行系统管理和维护。以下是一些必要的软件包

1. wget：用于下载文件和网页
2. vim：用于编辑文本文件
3. net-tools：用于管理网络配置
4. telnet：用于测试网络连接
5. lsof：用于查看系统打开的文件
6. tree：用于查看目录结构
7. htop：用于更好的查看进程

```shell
yum -y install  epel-release
yum -y install wget vim net-tools telnet lsof tree htop
```

# 配置SELinux

SELinux是CentOS7的安全模块，它可以提高系统的安全性。但是，如果不正确配置，它可能会导致一些问题。以下是一些常见的SELinux配置

```shell
# 一键关闭
## 永久关闭
sed -i 's/enforcing/disabled/' /etc/selinux/config

## 临时关闭
setenforce 0
```

# 关闭swap

```shell

## 临时关闭
swapoff -a

## 永久关闭
sed -ri 's/.*swap.*/#&/' /etc/fstab

# 关闭完swap后，一定要重启一下虚拟机！！！
```

# 时间同步

```shell
# 安装时间同步工具
yum install ntpdate -y

# 根据时间同步服务器更新时间
ntpdate time.windows.com
```

# 一键运行脚本

**包含更新软件源、安装必要软件包、关闭防火墙**

```shell

mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
vi /etc/yum.repos.d/CentOS-Base.repo
yum clean all
yum makecache
yum update -y
yum -y install  epel-release
yum -y install wget vim net-tools telnet lsof tree htop
systemctl stop firewalld.service
systemctl disable firewalld.service

```
