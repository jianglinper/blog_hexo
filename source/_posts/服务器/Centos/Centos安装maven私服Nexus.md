---
title: Centos 搭建 maven 私服 Nexus
categories:
  - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/c74ecef2cc97f72fb56454140b2432a8.png
main_color: '#B9ECCB'
tags:
  - Maven
  - Nexus
  - Centos
  - 食用教程
abbrlink: 9d28
date: 2024-10-29 00:16:00
---

> Nexus 是用来搭建 Maven 私服的，可以说是最好的免费工具了，它的官网上是这样说的：“世界上第一个也是唯一的免费使用的仓库解决方案”。目前的最新版本是 OSS 3.x。提供了针对 Windows、Unix、OS X 三种系统的版本。

这里只简单说明 Linux 下的安装方式，如果是下载的最新版本，它对系统内存和 jdk 版本是有要求的，要求内存大于`4G`，jdk 最低是`1.8`版本。

# 1. 安装Nexus

> 安装目录 /opt/Nexus

## 解压

~~~shell
tar -zxvf nexus-version-unix.tar.gz
~~~

1. 进入 `nexus-version/bin/` 目录，执行命令`./nexus start`
2. 访问 默认的 8081 端口即可

**没错，就是这么简单。**

通常情况下，以上三步就可以完成安装，如果有问题，并且提示“ NOT RECOMMENDED TO RUN AS ROOT ”，可以通过修改 `/bin/nexus` 文件，在其中加入 `RUN_AS_USER=root` ，然后重新启动即可。

# 2. 初识Nexus

> **proxy**：这是代理方式，它是用来代理中央仓库的，例如我们依赖的包在本地仓库没有，就会到私服获取，私服没有的话，会到中央仓库先把包下载到这里，然后再下载到本地仓库；
>
> **hosted**：指的是我们公司或团队内部的包，并且 hosted 类型的仓库会分为 releases 和 snapshots 两个，前者是正式版，后者一般是开发测试版；
>
> **group**：它的目的就是把多个仓库组合起来，然后我们项目中只需要配置上这个类型的仓库地址，就可以把它里面组合的几个仓库都关联上。

## 在项目中使用

### 获取依赖包

如果是普通项目成员，不需要向私服发布包的，只需要在 pom 文件中加入以下配置即可。

```xml
<repositories>
      <repository>
            <id>maven-public</id>
            <name>maven-public</name>
            <url>http://nexus.local:32768/repository/maven-public/</url>
            <snapshots>
               <enabled>true</enabled>
            </snapshots>
       </repository>
</repositories>
```

其中 id 要和你的仓库名称一致，url 就是私服的仓库地址，就是 type 为 group 的那个，它组合了一个 proxy 类型的和两个 hosted 类型的仓库。设置 snapshots 的 enabled 为ture，表示允许下载 snapshots 版本的包。

### 发布(deploy)包

1. 如果需要向仓库中发布包的，首先需要对仓库有权限才可以，没有权限的用户是没办法发布的。

打开 maven 的配置文件 `setting.xml` ，找到 `servers` 节点,然后在其中添加一个 `server` 节点。

用户名和密码也可以是管理员账号，或者管理员新创建的账号，制药对仓库有添加权限即可。

```xml
<server>
            <id>nexus-releases</id>
            <username>admin</username>
            <password>admin123</password>
        </server>

        <server>
            <id>nexus-snapshots</id>
            <username>admin</username>
            <password>admin123</password>
        </server>
```

请注意上面的 id，一会儿在 pom 文件中还会用到。

2. 然后需要在 pom 文件中配置下面的内容：

```xml
<distributionManagement>
        <repository>
            <id>nexus-releases</id>
            <name>maven-releases</name>
            <url>http://nexus.local:8081/repository/maven-releases/</url>
        </repository>
        <snapshotRepository>
            <id>nexus-snapshots</id>
            <name>maven-snapshots</name>
            <url>http://nexus.local:8081/repository/maven-snapshots/</url>
        </snapshotRepository>
</distributionManagement>
```

我们看到其中配置了一个 repository ，另外还有一个 snapshotRepository，上面表示的是 releases 版。同样注意 id 要和 setting.xml 里 server 下的 id 一致，不然会出现权限问题。url 分别指定 maven-releases 和 maven-snapshots 的地址。

只要在项目中配置 1.0-SNAPSHOT 这样，带有 SNAPSHOT 的就会到 snapshots 仓库，如果不加 SNAPSHOT 就会到 releases 仓库。

还有要注意的是，你要发布的包不应该是上级，也就是不能有 parent 依赖。否则在获取依赖的时候会有问题。

3. 最后执行 mvn clean deploy 命令，即可发布包到私服仓库中。

通过以上配置和操作，就可以完成包的发布过程。



参考文章 知乎[Maven 私服搭建指南](https://zhuanlan.zhihu.com/p/148636979)
