---
title: daemon.json 文件说明
categories:
  - 服务器
tags:
  - Docker
  - 食用教程
abbrlink: 7dbf
date: 2025-02-24 21:09:30
---

# daemon.json

这个文件是 Docker 的配置文件，下面是一些配置

## 工作目录

```json
"data-root": "/app/docker"
```


## 容器日志

```json
"log-opts": {"max-size":"500m", "max-file":"3"}
```

> max-size=500m，意味着一个容器日志大小上限是 500M
>
> max-file=3，意味着一个容器有三个日志，分别是 id+.json、id+1.json、id+2.json
>
> 注：设置后只对新添加的容器有效。


## 指定私服仓库地址

```json
"insecure-registries": [
	"http://harbor:30001"
]
```

## 镜像站点

```bash
"registry-mirrors": [
    "http://harbor:30001",
    "https:/docker.1panel.live"
  ]
```

