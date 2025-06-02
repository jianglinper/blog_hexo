---
title: 配置Jar.sh脚本 运行jar包
categories:
  - 服务器
tags:
  - Centos
  - SH脚本
  - 食用教程
  - java
abbrlink: f40e
date: 2025-01-01 14:15:00
---


> 经常遇到Linux部署jar包需要后台运行的情况
>
> 以下的脚本可以帮到大家

## 代码

```sh
#!/bin/bash

#author:wting（bug fixed version by suhai）

#这里替换为程序位置  #注意：APP_NAME为jar文件名（而非路径），且"="后面不能有空格
#此处的APP_NAME为打包后jar包名
#注意：如果包名携带日期或其他会变化的内容，例如xxxx-1.0.0.20221110.release.jar，请修改此处的名称为xxxx这类固定且能标识服务的名称，同时替换start方法中的启动命令
APP_NAME=gps-ruoyi-admin.jar
#添加jar包路径，方便在任何目录下都可以运行脚本启动服务
APP_PATH=/opt/hf-gps/jar

#使用说明，用来提示输入参数
usage() {
    echo "Usage: sh server.sh [start|stop|restart|status]"
    exit 1
}

#检查程序是否在运行
is_exist(){
  pid=`ps -ef|grep $APP_NAME|grep -v grep|awk '{print $2}'`
  #如果不存在返回1，存在返回0     
  if [ -z "${pid}" ]; then
   return 1
  else
    return 0
  fi
}

#启动命令执行后输出结果
start_log(){
  is_exist
  if [ $? -eq 0 ]; then
    echo "${APP_NAME} 启动成功！  pid=${pid}"
  else
    echo "${APP_NAME} 启动失败！请检查后重试"
  fi
}

#启动方法
start(){
  is_exist
  if [ $? -eq 0 ]; then
    echo "${APP_NAME} is already running. pid=${pid}"
  else
    nohup java -jar ${APP_PATH}/${APP_NAME} > /dev/null 2>&1 & 
    #如果APP_NAME携带可变内容，请替换下方启动命令，符号 * 表示jar包名称中变化的部分
    #nohup java -jar -Xmx512m -Xms512m ${APP_PATH}/${APP_NAME}*.jar >${APP_PATH}/web.log &
    start_log
  fi
}

#停止方法
stop(){
  is_exist
  if [ $? -eq "0" ]; then
    kill -9 $pid
    echo "${APP_NAME} 已关闭！ pid=${pid}"
  else
    echo "${APP_NAME} is not running"
  fi
  
  #该脚本的特殊性：确保关闭所有openoffice进程
  soffice_stop
}

#输出运行状态
status(){
  is_exist
  if [ $? -eq "0" ]; then
    echo "${APP_NAME} is running. Pid is ${pid}"
  else
    echo "${APP_NAME} is not running."
  fi
}

#重启
restart(){
  stop
  echo "${APP_NAME} 准备重启..."
  sleep 5
  start
}

#关闭openoffice
soffice_stop(){
  soffice_pid=`ps -e|grep soffice.bin |awk '{print $1}'`
  if [ -n "${soffice_pid}" ]; then
    kill -9 $soffice_pid
    echo "已关闭soffice.bin"
  fi
}


#根据输入参数，选择执行对应方法，不输入则执行使用说明
case "$1" in
  "start")
    start
    ;;
  "stop")
    stop
    ;;
  "status")
    status
    ;;
  "restart")
    restart
    ;;
  *)
    usage
    ;;
esac


```

## 命令

```shell
# 启动jar
sh server.sh start
# 查看jar状态
sh server.sh status
# 关闭jar
sh server.sh stop
# 重启jar
sh server.sh restart
```
