---
title: Centos 日志文件定期删除脚本
categories:
  - 服务器
tags:
  - Centos
  - SH脚本
  - 食用教程
abbrlink: '7343'
date: 2025-02-18 13:44:35
---

## 前言

在Linux系统的日常运维中，日志文件的不断累积会逐渐消耗宝贵的存储资源，甚至影响系统性能。面对日益增长的日志数据，定期清理已成为保障系统健康运行不可或缺的一环。本文将介绍如何高效地管理和定期清除Linux系统中的日志文件，确保您的服务器既能保持最佳性能，又能充分利用存储资源。

同时，需要确保清理日志的周期，不能清除近期的日志，以免造成无法追溯日志的情况。

## 正文

下面是一个Shell脚本

```shell
#! /bin/bash

#find：linux的查找命令，用户查找指定条件的文件；
#/opt/soft/log/：想要进行清理的任意目录；
#-mtime：标准语句写法；
#+3：查找3天前的文件，这里用数字代表天数；
#"*.log"：希望查找的数据类型，"*.jpg"表示查找扩展名为jpg的所有文件，"*"表示查找所有文件，这个可以灵活运用，举一反三；
#-exec：固定写法；
#rm -rf：强制删除文件，包括目录；
# {} \; ：固定写法，一对大括号+空格+\+;

echo "del file start....."

find /usr/local/huanfa-log/* -mtime +7 -path "*.log"  -exec rm -rf {} +  2>&1

echo "del file end"
```

## 定期运行脚本

使用crontab

```shell
crontab -e

# 每天的23:00（晚上11点）运行位于/app/clear-logfile.sh的脚本
0 23 * * * /app/clear-logfile.sh

crontab -l

# 赋于运行权限
sudo chmod +x /app/clear-logfile.sh
```
