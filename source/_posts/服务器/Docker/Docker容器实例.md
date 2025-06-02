---
title: Docker 容器实例
categories:
  - 服务器
tags:
  - Docker
abbrlink: b30e
date: 2025-02-25 10:44:10
---

## Portainer

Portainer 是一个用于管理 Docker 容器的开源工具，它允许用户通过一个直观的用户界面来管理和维护容器。

```bash
mkdir -p /opt/docker/portainer/data

docker run -d --restart=always --name="portainer" -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v /opt/docker/portainer/data:/data 6053537/portainer-ce
```
