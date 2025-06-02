---
title: Docker-compose实例 Elasticsearch
categories:
  - 服务器
tags:
  - Docker
  - Docker-Compose
  - Elasticsearch
abbrlink: 943e
date: 2025-02-24 21:33:08
---
# Docker运行Elasticsearch

# Elasticsearch

创建文件夹

```
mkdir /usr/local/elasticsearch/{config,data,log} -pv
    chown 1000:1000 /usr/local/elasticsearch/* -R
```

创建配置文件 主

```yaml
vim /usr/local/elasticsearch/config/es.yml

cluster.name: es-cluster
node.name: es-master
node.master: true
node.data: true


network.bind_host: 0.0.0.0
network.publish_host: 192.168.1.241
http.port: 9200
transport.tcp.port: 9300
http.cors.enabled: true
http.cors.allow-origin: "*"

discovery.zen.ping.unicast.hosts: ["es-master:9300", "es-node1:9300", "es-node2:9300"]
discovery.zen.minimum_master_nodes: 2
discovery.zen.ping_timeout: 5s

bootstrap.memory_lock: true
action.destructive_requires_name: true
cluster.initial_master_nodes: ["es-master"]

#ingest.geoip.downloader.enabled: false
```

docker-compose.yml

```yaml
version: '3'
services:
  es-master:
    image: harbor:59001/library/elasticsearch:7.6.2
    container_name: es-master
    environment:
      - "ES_JAVA_OPTS=-Xms4096m -Xmx4096m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    restart: always
    volumes:
      - /usr/local/elasticsearch/config/es.yml:/usr/share/elasticsearch/config/elasticsearch.yml:ro
      - /usr/local/elasticsearch/data:/usr/share/elasticsearch/data:rw
      - /usr/local/elasticsearch/log:/usr/share/elasticsearch/log:rw
    ports:
      - 9200:9200
      - 9300:9300
    :                        # 设置容器 hosts
      - "es-master:192.168.1.241"
      - "es-node1:192.168.1.250"
      - "es-node2:192.168.1.171"
```

创建配置文件 从

```yaml
vim /usr/local/elasticsearch/config/es.yml

cluster.name: es-cluster
node.name: es-node1
node.master: false
node.data: true

network.bind_host: 0.0.0.0
network.publish_host: 192.168.1.250
http.port: 9200
transport.tcp.port: 9300
http.cors.enabled: true
http.cors.allow-origin: "*"

discovery.zen.ping.unicast.hosts: ["es-master:9300", "es-node1:9300", "es-node2:9300"]
discovery.zen.minimum_master_nodes: 2
discovery.zen.ping_timeout: 5s

bootstrap.memory_lock: true
action.destructive_requires_name: true
cluster.initial_master_nodes: ["es-master"]
```

docker-compose.yml

```yaml
version: '3'
services:
  es-master:
    image: 192.168.1.63:59001/library/elasticsearch:7.6.2
    container_name: es-node2
    environment:
      - "ES_JAVA_OPTS=-Xms4096m -Xmx4096m"
    restart: always
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    volumes:
      - /usr/local/elasticsearch/config/es.yml:/usr/share/elasticsearch/config/elasticsearch.yml:ro
      - /usr/local/elasticsearch/data:/usr/share/elasticsearch/data:rw
      - /usr/local/elasticsearch/log:/usr/share/elasticsearch/log:rw
    ports:
      - 9200:9200
      - 9300:9300
    extra_hosts:                        # 设置容器 hosts
      - "es-master:192.168.1.241"
      - "es-node1:192.168.1.250"
      - "es-node2:192.168.1.171"
```
