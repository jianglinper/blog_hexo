---
title: Centos 安装 Kafka
categories:
  - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/76f9062f5c3fc5d8d9faa4daa195f1f9.png
main_color: '#95A1B0'
tags:
  - Kafka
  - Centos
  - 食用教程
abbrlink: d6fb
date: 2023-10-21 00:06:00
---

## 下载Kafka 

地址：https://kafka.apache.org/downloads

![image-20240118232846133](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007581.png)

## Kafka安装

因为选择下载的是 .zip 文件，直接跳过安装，一步到位。

选择在任一磁盘创建空文件夹（不要使用中文路径），解压之后把文件夹内容剪切进去（本人选择 D:\env-java\路径下，即完成安装）。

linux解压命令 `tar -zxvf kafka_2.13-xxx.tgz`，linux环境下指令是在\kafka_2.13-3.5.1\bin目录。

windows直接解压即可，windows环境下指令是在kafka_2.13-3.5.1\bin\windows目录。

注意：不同系统指令所在的目录不同。

执行命令当前目录D:\env-java\kafka_2.13-3.5.1

### 修改 kafka-server 和zookeeper配置

进入到目录：`kafka_2.13-3.5.1/config/server.properties`以及 `kafka_2.13-3.5.1/config/zookeeper.properties`

- linux系统:

  ```properties
  broker.id=1
  log.dir=/opt/kafka/logs
  ```
- windows系统:

  ```properties
  broker.id=1
  log.dirs=/env-java/kafka_2.13-3.5.1/kafka-logs
  # /：表示当前的根路径，即D盘。没有就会创建对应的文件夹。
  ```

## 启动 Kafka任务

### 启动ZooKeeper

1. linux系统:

```shell
bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
```

2. windows系统:

```shell
bin\windows\zookeeper-server-start.bat config\zookeeper.properties
```

### 启动kafka

1. linux系统:

```shell
bin/kafka-server-start.sh config/server.properties
```

2. windows系统:

```shell
bin\windows\kafka-server-start.bat config\server.properties
```

## 后台启动命令

```shell
# 1
cd /opt/kafka
nohup bin/kafka-server-start.sh config/server.properties 2>&1 &

# 2
cd /opt/kafka
bin/kafka-server-start.sh -daemon config/server.properties
```

## Kafka的使用

### 创建主题

1. linux系统:

```shell
bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic test
```

2. windows系统:

```shell
bin\windows\kafka-topics.bat --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic test
```

### 删除主题

1. linux系统:

```shell
bin/kafka-topics.sh --delete --bootstrap-server localhost:9092 --topic test
```

2. windows系统:

```shell
bin\windows\kafka-topics.bat --delete --bootstrap-server localhost:9092 --topic test
```

### 查看Topic 列表

1. linux系统:

```shell
bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```

2. windows系统:

```shell
bin\windows\kafka-topics.bat --list --bootstrap-server localhost:9092
```

### 启动 Producer

1. linux系统:

```shell
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
```

2. windows系统:

```shell
bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic test
```

### 启动 Consumer

1. linux系统:

```shell
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```

2. windows系统:

```shell
bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic test --from-beginning
```

### 查看Topic 相关信息（test）

1. linux系统:

```shell
bin/kafka-topics.sh --describe --bootstrap-server localhost:9092 --topic test
```

2. windows系统:

```shell
bin\windows\kafka-topics.bat --describe --bootstrap-server localhost:9092 --topic test
```

### 删除Topic 数据（test）

1. linux系统:

```shell
bin\windows\kafka-delete-records.sh--bootstrap-server localhost:9092 --offset-json-file \delete_script.json
```

2. windows系统:

```shell
bin\windows\kafka-delete-records.bat --bootstrap-server localhost:9092 --offset-json-file d:\delete_script.json
```

**delete_script.json文件内容为**

```shell
{
    "partitions": [
        {
            "topic": "test",
            "partition": 0,
            "offset": -1
        }
    ]
}
```
