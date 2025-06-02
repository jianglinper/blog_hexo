---
title: Kubesphere 安装 RabbitMQ集群
categories:
  - 服务器
tags:
  - K8S
  - Kubesphere
  - RabbitMQ
abbrlink: '3974'
date: 2024-12-30 08:56:31
---

# RabbitMQ

#### rabbitmq yaml资源文件

```shell
# /manifest/rabbitmq/*.yaml
kubectl create namespace rabbitmq-cluster

kubectl apply -f rabbitmq-cluster-operator.yml
kubectl apply -f rabbitmq-cluster.yaml
```

下载地址：
