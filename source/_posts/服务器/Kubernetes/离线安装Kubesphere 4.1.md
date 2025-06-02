---
title: 离线安装Kubesphere 4.1
categories:
  - 服务器
tags:
  - K8S
  - Kubesphere
abbrlink: 4f54
date: 2025-02-25 12:36:24
---

## 前言

本篇是离线安装`Kubernetes`和`Kubesphere`的教程，包含初始化镜像仓库`harbor` 导入必要镜像至镜像仓库

> Kubernetes版本：v1.26.12
>
> Kubesphere版本：4.1

参考教程：[离线安装 KubeSphere](https://www.kubesphere.io/zh/docs/v4.1/03-installation-and-upgrade/02-install-kubesphere/04-offline-installation/)

## 正文

### 1、安装依赖包

所有节点需要安装必要依赖

```shell
yum install -y socat conntrack
```

### 2、导入文件包并修改配置文件

以下是文件说明，已经放在网盘了，地址：

`config-sample.yaml`：集群配置文件

`create_project_harbor.sh`：初始化harbor项目脚本

`kk`：KubeKey工具

`ks-core-1.1.3.tgz`：helm安装包

`kubesphere-4.1.tar.gz`：Kubernetes离线包

将这些文件复制到主节点的工作目录下

```yaml

apiVersion: kubekey.kubesphere.io/v1alpha2
kind: Cluster
metadata:
  name: sample
spec:
  hosts:
  - {name: k8s-master, address: 192.168.1.20, internalAddress: 192.168.1.20, user: root, password: "root"}
  - {name: k8s-node1, address: 192.168.1.21, internalAddress: 192.168.1.21, user: root, password: "root"}
  - {name: k8s-node2, address: 192.168.1.22, internalAddress: 192.168.1.22, user: root, password: "root"}
  roleGroups:
    etcd:
    - k8s-master
    control-plane: 
    - k8s-master
    worker:
    - k8s-node1
    - k8s-node2
    registry: # 镜像仓库安装的位置
    - k8s-master 
  controlPlaneEndpoint:
    ## Internal loadbalancer for apiservers 
    # internalLoadbalancer: haproxy

    domain: lb.kubesphere.local
    address: ""
    port: 6443
  kubernetes:
    kubeletArgs: # 额外安装参数
      - --root-dir=/app/kubelet # 工作目录在/app下面，防止占满根目录
    version: v1.26.12
    clusterName: cluster.local
    autoRenewCerts: true
    containerManager: docker
  etcd:
    type: kubekey
  network:
    plugin: calico
    kubePodsCIDR: 10.233.64.0/18
    kubeServiceCIDR: 10.233.0.0/18
    ## multus support. https://github.com/k8snetworkplumbingwg/multus-cni
    multusCNI:
      enabled: false
  storage:
    openebs: # 安装openebs存储，这是基于local storage的一种存储，数据保存在本地
      basePath: /app/openebs/ # 指定安装位置
  registry: # 镜像仓库配置
    type: harbor
    auths:
      "dockerhub.kubekey.local":
        username: admin
        password: Harbor12345
        skipTLSVerify: true # 跳过tls验证
    privateRegistry: "dockerhub.kubekey.local" # 私有仓库配置
    namespaceOverride: "kubesphereio" # 默认命名空间
    registryMirrors: []
    insecureRegistries: []
  addons: []

```

### 3、创建镜像仓库（可选）

如果自己存在镜像仓库可以直接配置到config文件内（目前没有配置成功过，所以默认自己搭建一个仓库，将数据目录放到数据盘下）

**赋于kk执行权限**

```shell
sudo chmod +x kk
```

**执行创建仓库命令**

PS：此操作会在`registry` 节点安装Docker，docker-compose 等软件，并且将 harbor 文件放在/opt/harbor 目录下

```shell
./kk init registry -f config-sample.yaml -a kubesphere-4.1.tar.gz
```

安装完成后显示如下画面

![image-20250222213403587](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/image-20250222213403587.png)

验证容器是否启动，目录是否存在

![image-20250222213517765](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/image-20250222213517765.png)

#### 修改docker默认存储目录，Harbor数据目录（可选）

因为docker存储目录默认在 `/var/lib/docker/` 后面会把根目录塞满，需要将目录转移至数据盘下

**修改docker默认存储目录**

PS：此操作只建议在刚安装docker后操作

```shell
# 停止docker
systemctl stop docker
systemctl status docker

# 复制docker数据目录至/app/docker
cp -r /var/lib/docker/ /app/docker/

# 修改配置文件
vim /etc/docker/daemon.json
# 添加如下配置
"data-root": "/app/docker"

# 重启docker
systemctl daemon-reload
systemctl start docker

# 删除原目录
rm -rf /var/lib/docker/
```

```shell
# 修改harbor数据目录

# 修改harbor配置文件
cd /opt/harbor/
vim harbor.yml
# 56行   data_volume                  改成 指定目录
:56 data_volume: /mnt/registry -> data_volume: /app/registry

# 重新初始化harbor
# 如果启动出现文件，删除docker目录重启docker后再次运行
sh install.sh

# 删除原目录
rm -rf /mnt/registry
```

![启动成功](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/image-20250222214834331.png)

### 4、推送镜像至仓库

运行脚本创建harbor项目

```shell
chmod +x create_project_harbor.sh
sh create_project_harbor.sh
```

PS：如果使用别的仓库，需要修改如下配置

```shell
...

url="https://dockerhub.kubekey.local"  # 或修改为实际镜像仓库地址
user="admin"
passwd="Harbor12345"
...
```

**推送镜像至仓库（可选）**

此步骤只是验证镜像仓库是否安装成功，提前一步将镜像推送至仓库，安装集群时仍然会进行一次操作，验证所有镜像是否推送，所有此步骤可有可无

```shell
./kk artifact image push -f config-sample.yaml -a kubesphere-4.1.tar.gz
```

**推送成功**

![image-20250222220209143](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/image-20250222220209143.png)

### 5、创建Kubernetes集群

执行命令

```shell
./kk create cluster -f config-sample.yaml -a kubesphere-4.1.tar.gz --with-local-storage
```

> 指定 --with-local-storage 参数会默认部署 openebs localpv，如需对接其他存储，可在 Kubernetes 集群部署完成后自行安装。

在运行后会打印出所有节点的信息，确认无误后 输入 `yes` 进行安装

![image-20250222220302734](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/image-20250222220302734.png)

安装完成

![image-20250222221103082](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/image-20250222221103082.png)

### 6、安装Kubesphere

```shell
helm upgrade --install -n kubesphere-system --create-namespace ks-core ks-core-1.1.3.tgz \
     --set global.imageRegistry=dockerhub.kubekey.local/ks \
     --set extension.imageRegistry=dockerhub.kubekey.local/ks \
     --set ksExtensionRepository.image.tag=v1.1.2 \
     --debug \
     --wait
```

安装完成

![image-20250222221251185](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/image-20250222221251185.png)

显示了登录的地址和默认账号密码，可以直接登录

### 7、自己打包离线包（可选）

因为本文提供的镜像包是默认官方的，如果有其他需求请自己打包

需要准备一台可以联网的主机，无环境要求

将`manifest-sample.yaml`资源配置文件，`kk` 上传到运行目录

修改完成后运行如下命令，进行构建

```shell
./kk artifact export -m manifest-sample.yaml -o kubesphere.tar.gz
```

### 8、其他文件

其他文件官方获取方式：

**kk**

```shell
curl -sSL https://get-kk.kubesphere.io | sh -
```

**manifest-sample.yaml**

```shell
export KKZONE=cn

# 如需使用 kk 离线部署镜像仓库，添加 --with-registry 打包镜像仓库的安装文件
./kk create manifest --with-kubernetes v1.26.12 --with-registry
```

**ks-core-1.1.3.tgz**

```shell
# 安装 helm
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

# 下载 KubeSphere Core Helm Chart
VERSION=1.1.3     # Chart 版本
helm fetch https://charts.kubesphere.io/main/ks-core-${VERSION}.tgz
```

**config-sample.yam**

```shell
./kk create config --with-kubernetes v1.26.12
```
