---
title: 设置redis密码
categories:
  - 服务器
tags:
  - Redis
abbrlink: '7763'
date: 2024-12-30 08:56:31
---

安装Redis后，需要设置密码进行访问

PS：在设置完密码后，无密码也可以登录，但是无法获取数据，set数据

## 修改配置

```shell
# 修改配置文件
vim redis.conf

## :901 将requirepass 前面的# 去掉 后面设置成密码
## 例如：requirepass redis@@
```

设置完成
