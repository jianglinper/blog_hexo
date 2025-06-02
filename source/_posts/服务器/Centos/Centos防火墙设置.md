---
title: Centos 防火墙设置
categories:
   - 服务器
tags:
  - Centos
  - 食用教程
abbrlink: 62eb
date: 2025-01-15 00:01:00
---

个人观点：Centos防火墙没啥用处

- 在云服务商，机房会开通服务器业务
- 在个人家里，按需开放端口映射
- 在公司企业，对内部署网闸，内网防火墙，对外部部署外网防火墙

## 操作命令

```shell
# 查看运行情况
systemctl status firewalld.service
# 停止
systemctl stop firewalld.service
# 重启
systemctl restart firewalld.service
# 开启
systemctl start firewalld.service
# 关闭开机自启
systemctl disable firewalld.service
```

## 简单应用

1. 怎么开启一个端口

   添加

   ```shell
   firewall-cmd --zone=public --add-port=5005/tcp --permanent
   （--permanent永久生效，没有此参数重启后失效）
   ```

   添加端口外部访问权限（这样外部才能访问）

   ```shell
   firewall-cmd --add-port=5005/tcp
   ```

   重新载入，添加端口后重新载入才能起作用

   ```shell
   firewall-cmd --reload
   ```

   这些之后，端口是开启成功的，如果没有成功，重启系统试试。

### 2. 查看端口

```shell
firewall-cmd --zone=public --query-port=80/tcp
```

### 3. 删除端口

```shell
firewall-cmd --zone=public --remove-port=80/tcp --permanent
```

### 4. 查看firewall是否运行,下面两个命令都可以

```shell
systemctl status firewalld

firewall-cmd --state
```

### 5. 查看当前开了哪些端口

其实一个服务对应一个端口，每个服务对应/usr/lib/firewalld/services下面一个xml文件。

### 6. 查看开启了哪些服务

```shell
firewall-cmd --list-services
```

### 7. 查看开启了哪些端口

```shell
firewall-cmd --list-ports
```

### 8. 查看还有哪些服务可以打开

```shell
firewall-cmd --get-services
```

### 9. 查看所有打开的端口：

```shell
firewall-cmd --zone=public --list-ports
```

### 10.更新防火墙规则：

```shell
firewall-cmd --reload
```

### 11. 添加端口外部访问权限

```shell
 firewall-cmd --add-port=6379/tcp
```
