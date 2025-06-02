---
title: Centos 系统校时
categories:
  - 服务器
tags:
  - Centos
  - 食用教程
abbrlink: 143f
date: 2025-03-18 13:44:35
---


## 手动修改系统时间

```bash
timedatectl set-timezone "Asia/Shanghai"
date -s "2024-2-6 21:00:00"
```

## 自动校时

### 安装NTP校时服务

```bash
yum install ntp -y
```

### 配置NTP客户端

所有服务器先跟内外NTP服务器同步一下时间

```bash
ntpdate -u 172.0.0.1
```

把时间同步写入硬件中

```bash
hwclock -w
```

每台客户机设置一个定时任务，每小时同步一下时间。

```bash
crontab -e
*/60 * * * * /usr/sbin/ntpdate 172.0.0.1 >>/tmp/ntp.log
```

