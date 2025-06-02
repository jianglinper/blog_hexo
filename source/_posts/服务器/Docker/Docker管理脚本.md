---
title: Docker管理脚本
categories:
  - 服务器
tags:
  - Docker
abbrlink: '4581'
date: 2025-02-26 00:09:30
---


用于在多个目录中执行`docker-compose`命令，每个目录都包含一个`docker-compose.yml`文件。它支持四个动作：`status`、`start`、`stop`和`restart`。

- status 查询所有docker-compose状态
- start 开启多个 docker-compose
- stop 关闭多个 docker-compose
- restart 重启多个 docker-compose

```shell
#!/bin/bash

# 定义函数来执行不同的操作
function execute_docker_compose {
    local action=$1  # 第一个参数是动作（如 up, down, ps）
    local count=0    # 初始化目录计数器
    for dir in ./*/
    do
        if [ -f "${dir}docker-compose.yml" ]; then
         ((count++))
            cd "$dir"
            echo -e "Executing docker-compose $action in \033[32m$dir\033[0m"
            case "$action" in
                "start")
                    docker-compose up -d
                    ;;
                "stop")
                    docker-compose down
                    ;;
                "restart")
                    docker-compose down && docker-compose up -d
                    ;;
                "ps")
                    echo -e "NAME\t\tSERVICE\tSTATUS\t\tPORTS"
                    docker-compose ps --format "{{.Name}}\t{{.Service}}\t{{.Status}}\t{{.Ports}}"

                                  echo -e "\n"
                    ;;
                *)
                    echo "Unknown action: $action"
                    ;;
            esac
            cd - > /dev/null
       # else
            # echo "No docker-compose.yml found in $dir"
        fi
    done
     echo -e "Total directories processed: \033[31m$count\033[0m"  # 输出总处理目录数
}

# 检查是否提供了足够的参数
if [ $# -lt 1 ]; then
    echo "Usage: $0 {status|start|stop|restart}"
    exit 1
fi

# 根据提供的第一个参数选择操作
case "$1" in
    "status")
        execute_docker_compose "ps"
        ;;
    "start")
        execute_docker_compose "start"

        echo -e "\n\ndocker-compose up success!\ndocker-compose ps...\n"

        execute_docker_compose "ps"
        ;;
    "stop")
        execute_docker_compose "stop"
        ;;
    "restart")
        execute_docker_compose "restart"
        ;;
    *)
        echo "Unknown command: $1"
        echo "Usage: $0 {status|start|stop|restart}"
        exit 1
        ;;
esac

```
