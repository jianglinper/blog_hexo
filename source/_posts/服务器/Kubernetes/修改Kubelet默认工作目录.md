---
title: 修改Kubelet默认工作目录
categories:
  - 服务器
tags:
  - K8S
  - Kubesphere
abbrlink: 648e
date: 2024-12-30 08:56:31
---


## 前言

kubelet的默认工作目录（存储目录）是`/var/lib/kubelet`，会存放volume文件（包括emptyDir volume)、plugin文件等，默认挂载在系统盘。

而系统盘一般都不会太大，因此最好把kubelet工作目录更改到数据盘。

本文中，我们会把docker的工作目录从`/var/lib/kubelet`改到`/data/kubelet`，其中`/data`目录挂载了数据盘。

## 思路

想到两个方法：

- 修改配置法：拷贝数据到新目录，修改工作目录配置到新目录。
- 软链法：拷贝数据到新目录，使用新目录的软链替换原来的工作目录。不推荐，不知道有没有什么隐藏坑。

修改kubelet配置之前，为了保证不影响节点上的服务，最好先对节点操作禁止调度和驱逐。

## 正文

### 修改配置法

1、停止kubelet

```shell
systemctl stop kubelet
```

2、拷贝kubelet工作目录数据文件到新路径

```shell
mkdir -p /app/kubelet
cp -rf /var/lib/kubelet/pods /app/kubelet/
cp -rf /var/lib/kubelet/pod-resources /app/kubelet/
mv /var/lib/kubelet/pods{,.old}
mv /var/lib/kubelet/pod-resources{,.old}
```

> 注意，以下文件和目录一定要保留在老路径，不要移动和删除
>
> - /var/lib/kubelet/config.yaml
> - /var/lib/kubelet/kubeadm-flags.env
> - /var/lib/kubelet/pki
> - /var/lib/kubelet/device-plugins

3、添加或修改 /etc/sysconfig/kubelet 配置文件，添加`root-dir`参数

```shell
KUBELET_EXTRA_ARGS="--root-dir=/data/kubelet"
```

PS：如果是ubuntu系统，则要修改 /etc/default/kubelet 配置文件

4、重启kubelet

```shell
systemctl daemon-reload && systemctl restart kubelet
systemctl status kubelet
```

5、确认工作目录

```shell
ps -aux | grep kubelet | grep root-dir
```

PS：如果kubelet启动失败，可以通过查看kubelet详细日志进行排查。

```shell
journalctl -xu kubelet -r
```

6、清理旧工作目录（可选）

```shell
rm /var/lib/kubelet/pods.old -rf
rm /var/lib/kubelet/pod-resources.old -rf
```

## 软链法

1、停止kubelet

```shell
systemctl stop kubelet
```

2、拷贝kubelet数据文件到新路径

```shell
mkdir -p /data/kubelet
cp -rf /var/lib/kubelet/. /data/kubelet
mv /var/lib/kubelet /var/lib/kubelet.old
```

3、创建软链

```shell
ln -s /data/kubelet /var/lib/kubelet
```

4、启动kubelet

```shell
systemctl daemon-reload && systemctl restart kubelet
systemctl status kubelet
```

5、确认工作目录

```shell
ps -aux | grep kubelet | grep root-dir
```

6、清理旧工作目录（可选）

```shell
rm /var/lib/kubelet.old -rf
```

## kubeadm指定kubelet工作目录

与其临渴掘井，不如未雨绸缪。能否在使用kubeadm部署k8s集群的时候，直接指定好kubelet的工作目录？必须是可以的。

在执行`kubeadm init`之前，修改kubeadm.conf文件，添加kubeletExtraArgs字段。

```shell
apiVersion: kubeadm.k8s.io/v1beta3
kind: InitConfiguration
nodeRegistration:  
  kubeletExtraArgs:    
    root-dir: "/data/kubelet"
```

参考文档：

- [kubeadm Configuration - NodeRegistrationOptions](https://kubernetes.io/docs/reference/config-api/kubeadm-config.v1beta3/#kubeadm-k8s-io-v1beta3-NodeRegistrationOptions)
