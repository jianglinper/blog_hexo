---
title: MySQL添加外部访问权限
categories:
  - 服务器
tags:
  - MySQL
abbrlink: 647a
date: 2024-12-30 08:56:31
---

在Centos安装MySQL后，出现了在外网（非本机）无法访问MySQL的情况（MySQL错误码1130），由于MySQL的root用户默认权限是`localhost` 需要添加一个`root@%` 或者将权限改为`%`

本次直接将权限改为`%`

# 修改访问权限

```sql
mysql -u root -p 
--- 在服务器上登录进MySQL后

--- 选择mysql表
use mysql;
--- 查询root用户数据，可以看到host是localhost
select host from user where user='root';
--- 修改host为%
update user set host = '%' where user ='root';
--- 刷新配置
flush privileges;
```

成功登录
