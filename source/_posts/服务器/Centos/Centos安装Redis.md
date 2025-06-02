---
title: Centos 安装 Redis
categories:
  - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/080ee82692de805283a33e3e1e1db7b4.png
main_color: '#A42122'
tags:
  - Redis
  - Centos
  - 食用教程
abbrlink: 89c7
date: 2023-10-15 00:09:00
---

# 一、下载并解压Redis

## 1. 切换到/data目录

```
cd /data
```

## 2. 执行下面的命令下载redis：

```
wget https://download.redis.io/releases/redis-6.2.6.tar.gz
```

![image-20220413114109560](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008772.png)

## 3. 解压redis：

```
tar xzf redis-6.2.6.tar.gz
```

![image-20220413114133942](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008280.png)

## 4. 移动redis目录，一般都会将redis目录放置到 /usr/local/redis目录：

```
mv redis-6.2.6 /usr/local/redis
```

# 二、编译并安装redis

## 1. 进入redis安装目录，执行make命令编译redis：

```
cd /usr/local/redis
make
```

等待make命令执行完成即可

==如果执行make命令报错==：cc 未找到命令，原因是虚拟机系统中缺少gcc，执行下面命令安装gcc：

![image-20220413114309585](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072008882.png)

```
yum -y install gcc automake autoconf libtool make
```

如果执行make命令报错：致命错误:jemalloc/jemalloc.h: 没有那个文件或目录，则需要在make指定分配器为libc。执行下面命令即可正常编译：

![image-20220413114421767](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072009687.png)

```
make MALLOC=libc
```

make命令执行完，redis就编译完成了。

![image-20220413114806660](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072009006.png)

## 2. 执行下面命令安装redis，并指定安装目录

```
make install PREFIX=/usr/local/redis
```

![image-20220413114915652](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072009217.png)

至此，redis即安装成功。

# 三、启动Redis

## 1. 进入redis安装目录，执行下面命令启动redis服务

```
./bin/redis-server redis.conf
```


此时，可以看到redis服务被成功启动：

![image-20220413115051561](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072009483.png)

但这种启动方式不能退出控制台，如果退出，那么redis服务也会停止。

==Ctrl+C 停止运行==

## 2. 修改redis密码

进入到redis目录中

```
cd /usr/local/redis
```

修改**redis.conf**文件

```
vim redis.conf
```

找到第901行

![image-20220413120210330](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072009058.png)

把前面的#去掉 后面改成root

![image-20220413120236518](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072009003.png)

**i** 键插入 修改   :**wq** 保存并退出   **:q!**强行退出

## 3. 外部访问redis

进入到redis目录中

   ~~~
   cd /usr/local/redis
   ~~~

   修改**redis.conf**文件

   ~~~
   vim redis.conf
   ~~~

找到第75行

![image-20220413115537944](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072010828.png)

改成这个

![image-20220413121539670](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011397.png)

==**i** 键插入 修改   :**wq** 保存并退出   **:q!**强行退出==

开放端口

~~~
firewall-cmd --zone=public --add-port=6379/tcp --permanent
firewall-cmd --add-port=6379/tcp
firewall-cmd --reload
~~~




![image-20220413121605754](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072009035.png)


## 4. Redis以后台方式运行

进入到redis目录中

```
cd /usr/local/redis
```

修改**redis.conf**文件

```
vim redis.conf
```

找到第257行

![image-20220413120019384](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072009137.png)

no改成yes

![image-20220413120035867](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072009247.png)

**i** 键插入 修改   :**wq** 保存并退出   **:q!**强行退出

## 5. 通过下面命令查看redis进程，可以发现redis服务已经被启动了：

```
ps -ef | grep redis
```

![image-20220413142112828](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072009180.png)

## 6. 通过redis-cli测试redis是否可用，在redis安装目录执行下面命令：

```
./bin/redis-cli
```

![image-20220413121640174](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072009617.png)

致辞 redis安装完成

# 四、开机自启动

通过编辑脚本来实现开机自启。

## 1. 首先，新建一个系统服务文件：

```
vim /etc/systemd/system/redis.service
```

![image-20220413141401537](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072009024.png)

内容如下：

```
[Unit]
Description=redis-server
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/redis/bin/redis-server /usr/local/redis/redis.conf
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

## 2. 然后重载系统服务：

```
systemctl daemon-reload
```

## 3. 然后我们可以使用下面的命令对Redis进行一系列操作：

```
# 启动
systemctl start redis
# 停止
systemctl stop redis
# 重启
systemctl restart redis
# 查看状态
systemctl status redis
```

![image-20220413142412155](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072009401.png)

## 4. 执行下面的命令，可以让redis开机自启：

```
systemctl enable redis
```

![image-20220413142444684](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072009817.png)

## 5. 开机后执行下面命令可以看到Redis开机自启

```
ps -ef | grep redis
```

![image-20220413142518407](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072009165.png)

**总结**：这个方法适合有需要使用Redis频率高的场景，如果对系统性能有要求则不适用，因为后台运行需要消耗部分内存。
