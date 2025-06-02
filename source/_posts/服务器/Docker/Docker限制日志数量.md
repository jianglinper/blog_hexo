---
title: Docker限制日志数量
categories:
  - 服务器
tags:
  - Docker
abbrlink: bf88
date: 2025-02-26 08:44:33
---

## 前言

在Docker容器的日常运维中，日志管理是确保系统高效稳定运行的关键。未受控制的日志文件会迅速填满存储空间，影响系统性能甚至导致服务中断。为此，合理配置Docker以限制日志数量和设定最大存储天数显得尤为重要。本文将介绍具体的配置方法和最佳实践，帮助您有效管理Docker日志，释放存储资源，保障应用流畅运行。让我们马上开始优化您的容器环境吧。

## 正文

### docker配置文件（被动）

配置`daemon.json` 文件

```shell
vim /etc/docker/daemon.json

# 添加以下配置
"log-opts": {"max-size":"500m", "max-file":"3"}

# 重启docker服务
systemctl daemon-reload
systemctl restart docker
```

> max-size=500m，意味着一个容器日志大小上限是500M，
>
> max-file=3，意味着一个容器有三个日志，分别是id+.json、id+1.json、id+2.json
>
> 注：设置后只对新添加的容器有效。

### shell脚本删除docker日志（主动）

执行清除

```shell
#!/bin/bash

# Docker容器日志清理脚本 du -h --max-depth=1

# 设置Docker日志文件存储路径
log_path="/usr/local/docker/containers"

# 获取所有容器ID
container_ids=$(ls -1 $log_path)

# 循环处理每个容器
for container_id in $container_ids; do
    # 构造日志文件路径
    log_file="${log_path}/${container_id}/${container_id}-json.log"

    # 检查日志文件是否存在
    if [ -f "$log_file" ]; then
        echo "清理容器 ${container_id} 的日志文件: ${log_file}"
        
        # 清空日志文件
        truncate -s 0 "$log_file"
    else
        echo "未找到容器 ${container_id} 的日志文件: ${log_file}"
    fi
done

echo "日志清理完成。"
```
