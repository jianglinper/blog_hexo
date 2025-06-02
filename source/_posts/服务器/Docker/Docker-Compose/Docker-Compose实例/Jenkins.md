---
title: Docker-compose实例 jenkins
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
  - jenkins
abbrlink: 2e3f
date: 2025-02-24 21:33:08
---

# jenkins

docker-compose.yml

```yaml
version : '3'
services:
  jenkins:
    user: root
    restart: always
    image: jenkins/jenkins
    container_name: jenkins
    ports:
      - 7001:8080
    volumes:
      - /opt/jenkins/home/:/var/jenkins_home/
      - /var/run/docker.sock:/var/run/docker.sock
      - /usr/bin/docker:/usr/bin/docker
      - /etc/docker/daemon.json:/etc/docker/daemon.json
      - /opt/jdk1.8:/usr/local/java/jdk1.8.0_161
      - /opt/maven3:/usr/local/maven/maven3
      - /opt/node:/usr/local/node
      - /opt/jenkins/workspace:/usr/local/workspace

```

部署后端项目

**源码管理**

![image-20240511084012247](https://olinl-note.oss-cn-shanghai.aliyuncs.com/images/image-20240511084012247.png)

**触发器**

![image-20240511084032231](https://olinl-note.oss-cn-shanghai.aliyuncs.com/images/image-20240511084032231.png)

![image-20240511084240453](https://olinl-note.oss-cn-shanghai.aliyuncs.com/images/image-20240511084240453.png)

![image-20240511084249597](https://olinl-note.oss-cn-shanghai.aliyuncs.com/images/image-20240511084249597.png)

**Build**

![image-20240511084044273](https://olinl-note.oss-cn-shanghai.aliyuncs.com/images/image-20240511084044273.png)

**构建后操作**

![image-20240511084058484](https://olinl-note.oss-cn-shanghai.aliyuncs.com/images/image-20240511084058484.png)

![image-20240511084103606](https://olinl-note.oss-cn-shanghai.aliyuncs.com/images/image-20240511084103606.png)

```
Source files：huanfa-car-biz-service/huanfa-car-biz-service-business/target/*.jar
Remove prefix：huanfa-car-biz-service/huanfa-car-biz-service-business/target/
Remote directory：/opt/huanfav2/huanfa/huanfa-car-biz-service-business
Exec command：

cd /opt/huanfav2/huanfa/huanfa-car-biz-service-business
source /etc/profile
bash server.sh restart
```
