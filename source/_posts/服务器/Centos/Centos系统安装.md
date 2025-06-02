---
title: Centos 系统安装
categories:
  - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/cb2b31b7c34cc47a9f208a7be9cbd32d.png
main_color: '#E2AECB'
tags:
  - Centos
  - 食用教程
abbrlink: '5068'
date: 2022-08-10 00:13:00
---

下文主要介绍使用Vmware软件安装一个centos7.9的linux虚拟机（非虚拟机安装，安装过程基本相同，这里不做说明）。正常安装好Vmware软件后，就直接按照以下步骤进行。

# 一、 配置虚拟机

## 1. 创建新的虚拟机

![image-20220413084707493](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006968.png)

## 2. 选择“典型”，点击下一步

![image-20220413084746611](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006527.png)

## 3. 选择稍后安装

==（如果选择安装程序光盘映像文件，则会按照最小系统自动安装）==

![image-20220413084827622](src/image-20220413084827622.png)

## 4. 选择Linux操作系统，版本选择Centos7 64位

![image-20220413085149709](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006061.png)

## 5. 设置主机名、虚拟机存放位置

![image-20220413085402406](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006108.png)

## 6. 虚拟磁盘设置，磁盘大小和实际计算机硬盘大小无关系。

==建议选择分为单个文件==

![image-20220413085506300](src/image-20220413085506300.png)

## 7. 点击自定义硬件

![image-20220413085625000](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006728.png)
![image-20220413085714818](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006349.png)
![image-20220413085750301](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006392.png)
![image-20220413085845315](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006753.png)
![image-20220413090005394](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006249.png)

## 8. 然后点击完成

![image-20220413090045474](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006790.png)

# 二、安装Centos7.9

## 1. 点击开启此虚拟机

![image-20220413090249237](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006102.png)

## 2. 第一行安装 Centos7

![image-20220413090329810](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006301.png)

## 3. 跑码进行GUI界面

![image-20220413090430296](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006646.png)

![image-20220413090436071](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006102.png)

![image-20220413090441255](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006702.png)

![image-20220413090458544](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006428.png)

## 4. 第一个滑到最下面选择中国 点击==继续==

![image-20220413090544721](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006102.png)

## 5. 安装设置

**选择软件里的软件选择**

![image-20220413090646519](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006463.png)

选择最小安装 系统管理工具  点击完成

![image-20220413090728909](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006345.png)

滑到最下面选择系统安装位置

![image-20220413090856082](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006922.png)

选择硬盘 自动配置分区 然后点击完成

![image-20220413090938987](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006523.png)

**点击网络和主机名**

![image-20220413091046294](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072006138.png)

编辑主机名

![image-20220413091136964](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007584.png)

点击配置网络

![image-20220413091201116](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007002.png)

==如果使用固定ip==

查看一下以太网的配置 这里 是100.0.0.0 那么一会我们就输入==100.0.0.(大于2的数字)==

![image-20220413091504209](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007437.png)

![image-20220413092306772](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007872.png)

选择IPv4设置 方法是手动 地址新增 输入==100.0.0.(大于2的数字)== 上面的 子网掩码输入上面查到的信息  DNS服务器输入8.8.8.8

![image-20220413092402931](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007306.png)

==自动获取IP地址就不需要设置网络信息了==

点击开启以太网 就可以查看获取到的ip地址

![image-20220413092452335](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007613.png)

![image-20220413092618201](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007974.png)

## 6. 点击开始安装 进行安装

![image-20220413092932475](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007237.png)

## 7. 设置root密码 和创建用户 也可以不创建

![image-20220413093005837](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007041.png)

![image-20220413093100278](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007439.png)

## 8. 安装完成

![image-20220413093207805](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072007128.png)
