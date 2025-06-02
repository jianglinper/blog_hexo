---
title: MySQL问题大全
categories:
  - 服务器
tags:
  - MySQL
abbrlink: bf3
date: 2024-12-30 08:56:31
---


# mysql提示缺少依赖libaio.so.1的问题

linux上用rpm安装 mysql-community-server-5.7.23-1.el7.x86_64.rpm提示缺少依赖

```javascript
error: Failed dependencies:
	libaio.so.1()(64bit) is needed by mysql-community-server-5.7.23-1.el7.x86_64
	libaio.so.1(LIBAIO_0.1)(64bit) is needed by mysql-community-server-5.7.23-1.el7.x86_64
	libaio.so.1(LIBAIO_0.4)(64bit) is needed by mysql-community-server-5.7.23-1.el7.x86_64
```

重新安装

```cobol
rpm -ivh libaio-0.3.109-13.el7.i686.rpm
```

# Public Key Retrieval is not allowed

连接MySQL数据库的时候，报错内容如下：“Public Key Retrieval is not allowed”。

mysql 8.0 默认使用 `caching_sha2_password` 身份验证机制 （`即从原来mysql_native_password` 更改为 `caching_sha2_password`。）

从 5.7 升级 8.0 版本的不会改变现有用户的身份验证方法，但新用户会默认使用新的 `caching_sha2_password` 。 客户端不支持新的加密方式。 修改用户的密码和加密方式。

## 方案一：
在命令行模式下进入`mysql`，输入以下命令:

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
或者 

ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'root';
```

然后就可以正常连接了。

## 方案二：

在配置数据源的时候直接将属性`allowPublicKeyRetrieval`设置为`true`即可
