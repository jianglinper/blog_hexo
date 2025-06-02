---
title: K8s 修改数据目录
categories:
  - 服务器
tags:
  - K8S
  - Kubesphere
abbrlink: 89eb
date: 2024-12-30 08:56:31
---

> 参考网址：[kubelet修改工作目录 | 好好学习的郝](https://www.voidking.com/dev-kubelet-root-dir/)

## 需求描述

kubelet的默认工作目录（存储目录）是`/var/lib/kubelet`，会存放volume文件（包括emptyDir volume)、plugin文件等，默认挂载在系统盘。
而系统盘一般都不会太大，因此最好把kubelet工作目录更改到数据盘。
本文中，我们会把docker的工作目录从`/var/lib/kubelet`改到`/app/kubelet`，其中`/app`目录挂载了数据盘。

## 修改配置

“建议先修改node节点的，再修改主节点的”

1. 停止kubelet

```shell
systemctl stop kubelet
```

2. 拷贝kubelet工作目录数据文件到新路径

```shell
mkdir -p /app/kubelet  
cp -rf /var/lib/kubelet/pods /app/kubelet/  
cp -rf /var/lib/kubelet/pod-resources /app/kubelet/  
mv /var/lib/kubelet/pods{,.old}  
mv /var/lib/kubelet/pod-resources{,.old}
```

注意，以下文件和目录一定要保留在老路径，不要移动和删除

- /var/lib/kubelet/config.yaml
- /var/lib/kubelet/kubeadm-flags.env
- /var/lib/kubelet/pki
- /var/lib/kubelet/device-plugins

3. 添加或修改 /etc/sysconfig/kubelet 配置文件，添加`root-dir`参数

```shell
KUBELET_EXTRA_ARGS="--root-dir=/app/kubelet"
```

PS：如果是ubuntu系统，则要修改 /etc/default/kubelet 配置文件
4. 重启kubelet

```shell
systemctl daemon-reload && systemctl restart kubelet
systemctl status kubelet
```

6. 清理旧工作目录（可选）

```shell
rm /var/lib/kubelet/pods.old -rf  
rm /var/lib/kubelet/pod-resources.old -rf
```
