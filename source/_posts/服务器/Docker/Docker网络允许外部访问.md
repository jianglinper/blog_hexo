---
title: Docker网络允许外部访问
categories:
  - 服务器
tags:
  - Docker
abbrlink: ba30
date: 2025-02-24 09:33:08
---

```shell

# 开放
iptables -A FORWARD -j ACCEPT


yum install iptables-services
sudo service iptables save
systemctl enable iptables 
systemctl start iptables 


```
