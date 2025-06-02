---
title: Centos 安装代理服务器 Squid
categories:
  - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/26e1ba9fe402cdaf0765e85b85b06659.png
main_color: '#FF7A24'
tags:
  - Squid
  - Centos
  - 食用教程
abbrlink: a25e
date: 2024-10-09 00:03:00
---



# 代理squid

在有外网服务器上

~~~shell
yum install -y squid
systemctl start squid

~~~



在无外网服务器上

~~~shell

export http_proxy=http://192.168.0.217:3128
export https_proxy=http://192.168.0.217:3128

export no_proxy="127.0.0.1, localhost, 192.168.1.85，192.168.1.241,192.168.1.63,192.168.1.58,192.168.1.93,192.168.1.116,192.168.1.250,192.168.1.171,192.168.1.139"

127.0.0.1 | localhost | 192.168.1.85 | 192.168.1.241 | 192.168.1.63 | 192.168.1.58 | 192.168.1.93 | 192.168.1.116 | 192.168.1.250 | 192.168.1.171 | 192.168.1.139

source /etc/profile
echo $http_proxy


-Dhttp.proxyHost=192.168.1.63 -Dhttp.proxyPort=3128 -Dhttps.proxyHost=192.168.1.63 -Dhttps.proxyPort=3128



export http_proxy=http://192.168.0.217:3128
export https_proxy=http://192.168.0.217:3128
export no_proxy="127.0.0.1, localhost, 192.168.0.217,192.168.0.196"
~~~

