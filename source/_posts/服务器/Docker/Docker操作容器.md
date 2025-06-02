---
title: Docker操作命令之 操作容器
categories:
  - 服务器
tags:
  - Docker
  - 食用教程
abbrlink: bb89
date: 2025-02-25 09:12:33
---

参考：[Docker 命令大全 | 菜鸟教程](https://www.runoob.com/docker/docker-command-manual.html)

## Docker run

docker run 命令用于创建并启动一个新的容器。

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

常用参数说明：

*  **`-d`**: 后台运行容器并返回容器 ID。
*  **`-it`**: 交互式运行容器，分配一个伪终端。
*  **`--name`**: 给容器指定一个名称。
*  **`-p`**: 端口映射，格式为 `host_port:container_port`。
*  **`-v`**: 挂载卷，格式为 `host_dir:container_dir`。
*  **`--rm`**: 容器停止后自动删除容器。
*  **`--env`** **或**  **`-e`**: 设置环境变量。
*  **`--network`**: 指定容器的网络模式。
*  **`--restart`**: 容器的重启策略（如 `no`、`on-failure`、`always`、`unless-stopped`）。
*  **`-u`**: 指定用户。

## Docker start/stop/restart

**docker start** 命令用于启动一个或多个已经创建的容器。

**docker stop** 命令用于停止一个运行中的容器。

**docker restart** 命令用于重启容器。

```bash
docker start [OPTIONS] CONTAINER [CONTAINER...]
```

**参数**

*  **`-a`**: 附加到容器的标准输入输出流。
*  **`-i`**: 附加并保持标准输入打开。



```bash
docker stop [OPTIONS] CONTAINER [CONTAINER...]
docker restart [OPTIONS] CONTAINER [CONTAINER...]
```

**参数**

 **-t, --time**: 停止容器之前等待的秒数，默认是 10 秒。

## Docker exec

`docker exec` 命令用于在运行中的容器内执行一个新的命令。这对于调试、运行附加的进程或在容器内部进行管理操作非常有用。

```bash
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

### 常用参数

*  **`-d, --detach`**: 在后台运行命令。
*  **`--detach-keys`**: 覆盖分离容器的键序列。
*  **`-e, --env`**: 设置环境变量。
*  **`--env-file`**: 从文件中读取环境变量。
*  **`-i, --interactive`**: 保持标准输入打开。
*  **`--privileged`**: 给这个命令额外的权限。
*  **`--user, -u`**: 以指定用户的身份运行命令。
*  **`--workdir, -w`**: 指定命令的工作目录。
*  **`-t, --tty`**: 分配一个伪终端。



在运行中的 my\_container 容器内执行 ls /app 命令，列出 /app 目录的内容。

以交互模式运行命令：

```
docker exec -it my_container /bin/bash
```



## Docker ps

docker ps 命令用于列出 Docker 容器。

默认情况下，docker ps 命令只显示运行中的容器，但也可以通过指定选项来显示所有容器，包括停止的容器。

```bash
docker ps [OPTIONS]
```

OPTIONS说明：

*  **`-a, --all`**: 显示所有容器，包括停止的容器。
*  **`-q, --quiet`**: 只显示容器 ID。
*  **`-l, --latest`**: 显示最近创建的一个容器，包括所有状态。
*  **`-n`**: 显示最近创建的 n 个容器，包括所有状态。
*  **`--no-trunc`**: 不截断输出。
*  **`-s, --size`**: 显示容器的大小。
*  **`--filter, -f`**: 根据条件过滤显示的容器。
*  **`--format`**: 格式化输出。

### 实例

**1、列出所有在运行的容器信息**

默认情况下，docker ps 只显示正在运行的容器。

```bash
docker ps
CONTAINER ID   IMAGE          COMMAND                ...  PORTS                    NAMES
09b93464c2f7   nginx:latest   "nginx -g 'daemon off" ...  80/tcp, 443/tcp          myrunoob
96f7f14e99ab   mysql:5.6      "docker-entrypoint.sh" ...  0.0.0.0:3306->3306/tcp   mymysql

```

输出详情介绍：

**CONTAINER ID:**  容器 ID。

**IMAGE:**  使用的镜像。

**COMMAND:**  启动容器时运行的命令。

**CREATED:**  容器的创建时间。

**STATUS:**  容器状态。

状态有7种：

* created（已创建）
* restarting（重启中）
* running（运行中）
* removing（迁移中）
* paused（暂停）
* exited（停止）
* dead（死亡）

**PORTS:**  容器的端口信息和使用的连接类型（tcp\\udp）。

**NAMES:**  自动分配的容器名称。



## Docker logs

`docker logs` 命令用于获取和查看容器的日志输出。

`docker logs` 命令非常有用，可以帮助用户调试和监控运行中的容器。

```bash
docker logs [OPTIONS] CONTAINER
```

常用选项：

*  **`-f, --follow`**: 跟随日志输出（类似于 `tail -f`）。
*  **`--since`**: 从指定时间开始显示日志。
*  **`-t, --timestamps`**: 显示日志时间戳。
*  **`--tail`**: 仅显示日志的最后部分，例如 `--tail 10` 显示最后 10 行。
*  **`--details`**: 显示提供给日志的额外详细信息。
*  **`--until`**: 显示直到指定时间的日志。

### 实例

**显示容器日志**

```
docker logs my_container
```

显示名为 my\_container 的容器的所有日志，输出内容：

```
hello world
hello world
hello world
...
```

**跟随日志输出**

```
docker logs -f my_container
```

持续显示 my\_container 的日志输出，输出内容：

```
hello world
hello world
hello world
...
```

**显示带时间戳的日志**

```
docker logs -t my_container
```

显示包含时间戳的日志，输出内容：

```
2023-07-22T15:04:05.123456789Z hello world
2023-07-22T15:04:06.123456789Z hello world
2023-07-22T15:04:07.123456789Z hello world
...
```

**从指定时间开始显示日志**

```

docker logs --since="2023-07-22T15:00:00" my_container
```

显示 2023-07-22T15:00:00 之后的日志。

**显示最后 10 行日志**

```
docker logs --tail 10 my_container
```

显示 my\_container 的最后 10 行日志。

**显示额外详细信息的日志**

```
docker logs --details my_container
```

显示 my\_container 的日志，并包含额外详细信息。

**显示直到指定时间的日志**

```
docker logs --until="2023-07-22T16:00:00" my_container
```

显示 2023-07-22T16:00:00 之前的日志。
