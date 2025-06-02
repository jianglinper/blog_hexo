---
title: Centos7 查看是否链接外网 开启外网
categories:
  - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/6f7ae7c4b53225dfee1074d59160671e.png
main_color: '#A7CBFD'
tags:
  - 网络
  - Centos
abbrlink: ed95
date: 2022-08-10 00:12:00
---

# Linux查看是否链接外网

## 1. 登入 Linux

## 2. ping 百度官网

   ~~~
   ping www.baidu.com
   ~~~

## 3. 出现以下界面就代表已连接外网

   ==Ctrl+C停止任何指令 包括Ping==

   ​	![image-20220413100332915](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011197.png)

## 4. 如果出现这种界面就是没有链接外网

   ![image-20220413100447439](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011242.png)

# CentOS7 开启网络连接

## 1. 打开/etc/sysconfig/network-scripts文件夹。

   ~~~
   cd /etc/sysconfig/network-scripts
   ~~~

   ![image-20220413100747819](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011378.png)

## 2. 输入ls命令，查看network-scripts目录下所有文件。

   ~~~
   ls
   ~~~

   

   ![image-20220413100702009](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011933.png)

## 3. 输入vi ifcfg-enps33，编辑ifcfg-enps33文件。

   ~~~
   vi ifcfg-ens33
   ~~~

   ![image-20220413100929879](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011551.png)

## 4. 按i进入编辑模式

   ![image-20220413101023636](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011167.png)

## 5. 将ONBOOT设置为yes，按ESC键，输入:wq命令，保存文件并退出。

   ![image-20220413101056019](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011572.png)

   ![image-20220413101105796](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011187.png)

## 6. 输入service network restart命令，重启网络。

   ~~~
   service network restart
   ~~~

   ![image-20220413101325066](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011924.png)



# Linux下进行静态IP的设置

## 1.虚拟机的网络模式

###   1.1 桥接模式

​		     桥接模式就是将主机网卡与虚拟机虚拟的网卡利用虚拟网桥进行通信。类似于把物理主机虚拟为一个交换机，相互可以访问而不干扰。在桥接模式下，虚拟机ip地址需要与主机在同一个网段，如果需要联网，则网关与DNS需要与主机网卡一致

##  1.2 NAT（地址转换模式）

​			  在NAT模式中，主机网卡直接与虚拟NAT设备相连，利用虚拟的NAT设备以及虚拟DHCP服务器来使虚拟机连接外网。

###  1.3 仅主机模式

​			仅主机模式其实就是NAT模式去除了虚拟NAT设备，将虚拟机与外网隔开，使得虚拟机成为一个独立的系统，只与主机相互通讯。

## 2.配置NAT模式

2.1 在虚拟机菜单栏的“编辑”窗口，选择打开虚拟网络编辑器；

​	![image-20220413101816562](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011438.png)

2.2 选择使用NAT模式，可以设置“NAT设置”和“DHCP设置”；



​	![image-20220413101846241](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011023.png)

2.3 点击“NAT设置”，打开如下界面。记住设置的网关IP，在后面配置LinuxIP静态地址时会用到，网关IP地址应与上图中的子网IP地址处于同一网段；

​	![image-20220413101938954](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011657.png)

2.4 点击“DHCP设置”，打开如下界面，可设置：开始IP地址以及结束IP地址，其中192.60.30.255一般用做广播地址，这方面的知识可参考IP地址的设置；

![image-20220413101951537](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011959.png)

2.5 完成以上设置后，点击“确定”，保存即可。

# 3.Linux系统配置静态IP地址

3.1 使用dhclient命令（dhclient：获取IP地址），也使用ip addr命令（也可使用ifconfig命令，来查看IP地址）；

~~~
ip add
~~~

​	![image-20220413102104965](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011133.png)
3.2修改网关配置信息

## 1. 打开/etc/sysconfig/network-scripts文件夹。

   ~~~
   cd /etc/sysconfig/network-scripts
   ~~~

   ![image-20220413100747819](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011843.png)

## 2. 输入ls命令，查看network-scripts目录下所有文件。

   ~~~
   ls
   ~~~

   

   ![image-20220413100702009](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011044.png)

## 3. 输入vi ifcfg-enps33，编辑ifcfg-enps33文件。

   ~~~
   vi ifcfg-ens33
   ~~~

   ![image-20220413100929879](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011112.png)

## 4. 按i进入编辑模式

   ![image-20220413101023636](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072011911.png)

## 5. 修改配置文件

   ![image-20220413102322812](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072012397.png)

   最下面的红框为追加命令：

   ~~~
   IPADDR=192.168.222.130 # ip地址
   NETMASK=255.255.255.0 # 固定值 子网掩码
   GATEWAY=192.168.222.2 # 网关地址
   DNS1=8.8.8.8		# DNS地址
   ~~~

3.2 使用：systemctl restart network.service 命令，重启网络服务；
	

~~~
systemctl restart network.service
~~~

   


3.3 使用ping命令，查看虚拟机是否已经连接到网络；

~~~
ping www.baidu.com
~~~

​	![image-20220413100332915](https://olinl-note.oss-cn-shanghai.aliyuncs.com/note/202402072012395.png)
