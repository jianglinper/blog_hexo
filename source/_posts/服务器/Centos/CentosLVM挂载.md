---
title: Centos LVM挂载
categories:
  - 服务器
cover: >-
  https://pan.jianglin.cc:8443/d/images/2025/06/01/f8cf11ea06e6c25ccdf9568677b9cb53.png
main_color: '#E5B5E5'
tags:
  - LVM
  - Centos
abbrlink: '2e58'
date: 2024-10-09 00:02:00
---


> 说明：此操作在centos7下进行，如果是centos6发行版，需要注意格式化LV的文件系统类型（centos7.0开始默认文件系统是xfs，centos6是ext4）、最后一步写入系统的类型

# 1、查看当前磁盘

```shell
[root@vm ~]# df -Th                                                     

Filesystem              Type      Size  Used Avail Use% Mounted on
/dev/mapper/centos-root xfs        44G  3.7G   41G   9% /
devtmpfs                devtmpfs  7.8G     0  7.8G   0% /dev
tmpfs                   tmpfs     7.8G     0  7.8G   0% /dev/shm
tmpfs                   tmpfs     7.8G  777M  7.0G  10% /run
tmpfs                   tmpfs     7.8G     0  7.8G   0% /sys/fs/cgroup
/dev/vda1               xfs      `1014M  179M  836M  18% /boot
tmpfs                   tmpfs     1.6G   36K  1.6G   1% /run/user/0
tmpfs                   tmpfs     1.6G   40K  1.6G   1% /run/user/1001
```

# 2、查看块分区

```shell
[root@vm ~]# lsblk 

NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
vda             252:0    0   50G  0 disk 
├─vda1          252:1    0    1G  0 part /boot
└─vda2          252:2    0   49G  0 part 
  ├─centos-root 253:0    0   44G  0 lvm  /
  └─centos-swap 253:1    0    5G  0 lvm  [SWAP]
vdb             252:16   0  974G  0 disk
```

可以看到 `/dev/vdb` 为新增的磁盘

# 3、格式化磁盘

```shell
# 使用Parted对 vdb硬盘进行分区
parted /dev/vdb

# 查看该硬盘分区情况
print

# 转为GPT模式
mklabel gpt

# 创建主分区 大小位1045GB
mkpart primary 0 1045GB

# 设置1分区为lvm标签
set 1 lvm on

# 退出工具
quit
```

**案例：**

```shell
[root@vm ~]# parted /dev/vdb

GNU Parted 3.1
Using /dev/vdb
Welcome to GNU Parted! Type 'help' to view a list of commands.

(parted) print

Error: /dev/vdb: unrecognised disk label
Model: Virtio Block Device (virtblk)                                      
Disk /dev/vdb: 1046GB
Sector size (logical/physical): 512B/512B
Partition Table: unknown
Disk Flags: 

(parted) mklabel gpt    

(parted) mkpart primary 0 1045GB

Warning: The resulting partition is not properly aligned for best performance.
Ignore/Cancel? i    

(parted) print   

Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 1046GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name     Flags
 1      17.4kB  1045GB  1045GB               primary

(parted) set 1 lvm on    

(parted) print

Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 1046GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name     Flags
 1      17.4kB  1045GB  1045GB               primary  lvm

(parted) quit   

Information: You may need to update /etc/fstab

```

以上 (parted) 开头的代表输入的命令，具体功能可以通过 help 查看

# 4、重读分区表

```shell
partprobe /dev/vdb
```

# 5、重新查看块分区

```shell
[root@vm ~]# lsblk 
NAME            MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
vda             252:0    0    50G  0 disk 
├─vda1          252:1    0     1G  0 part /boot
└─vda2          252:2    0    49G  0 part 
  ├─centos-root 253:0    0    44G  0 lvm  /
  └─centos-swap 253:1    0     5G  0 lvm  [SWAP]
vdb             252:16   0   974G  0 disk 
└─vdb1          252:17   0 973.2G  0 part
```

可以看到vdb下多了vdb1，这个用来创建pv

# 6、创建PV

```shell
pvcreate -v /dev/vdb1
```

**案例：**

```shell
[root@vm ~]# pvcreate -v /dev/vdb1
    Wiping internal VG cache
    Wiping cache of LVM-capable devices
    Wiping signatures on new PV /dev/vdb1.
    Set up physical volume for "/dev/vdb1" with 2041015592 available sectors.
    Zeroing start of device /dev/vdb1.
    Writing physical volume data to disk "/dev/vdb1".
  Physical volume "/dev/vdb1" successfully created.
```

### 注意

PV创建好之后，到这一步可以选择扩展或者新建挂载点，**扩容的前提条件是扩容挂载点的磁盘格式是LVM格式**，这里先演示新建挂载点然后删除挂载点并扩容

# 7、新增LVM挂载

## 查看PV

```shell
[root@vm ~]# pvdisplay
  --- Physical volume ---
  PV Name               /dev/vda2
  VG Name               centos
  PV Size               <49.00 GiB / not usable 3.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              12543
  Free PE               0
  Allocated PE          12543
  PV UUID               CCUeq0-ZnG9-iUY8-dPOj-fVOa-XC10-ooGyle
   
  "/dev/vdb1" is a new physical volume of "973.23 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/vdb1
  VG Name               
  PV Size               973.23 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               vUoC7C-MIP2-iwXr-0SWu-c2Wz-yb6o-qFrfo1
```

注意看 VG Name，前面的是系统安装时选择LVM格式的，后面的还没有创建，所以 VG Name 为空

## 新建VG

```shell
[root@vm ~]# vgcreate -s 4M vg01 /dev/vdb1  
  Volume group "vg01" successfully created
```

## 查看VG

```shell
[root@vm ~]# vgdisplay  
  --- Volume group ---  
  VG Name               vg01  
  System ID               
  Format                lvm2  
  Metadata Areas        1  
  Metadata Sequence No  1  
  VG Access             read/write  
  VG Status             resizable  
  MAX LV                0  
  Cur LV                0  
  Open LV               0  
  Max PV                0  
  Cur PV                1  
  Act PV                1  
  VG Size               973.23 GiB  
  PE Size               4.00 MiB  
  Total PE              249147  
  Alloc PE / Size       0 / 0     
  Free  PE / Size       249147 / 973.23 GiB  
  VG UUID               hb8zoy-kKjU-VXD5-EjCN-8hS2-MOzf-lq3O5j  
     
  --- Volume group ---  
  VG Name               centos  
  System ID               
  Format                lvm2  
  Metadata Areas        1  
  Metadata Sequence No  3  
  VG Access             read/write  
  VG Status             resizable  
  MAX LV                0  
  Cur LV                2  
  Open LV               2  
  Max PV                0  
  Cur PV                1  
  Act PV                1  
  VG Size               <49.00 GiB  
  PE Size               4.00 MiB  
  Total PE              12543  
  Alloc PE / Size       12543 / <49.00 GiB  
  Free  PE / Size       0 / 0     
  VG UUID               kSwsMj-4FKB-zwFq-7MBq-EfOD-rccg-HC59x9
```

可以看到 vg01 为新建的VG

## 新建LV

```shell
[root@vm ~]# lvcreate  -l 100%FREE -n lv01 vg01  
  Logical volume "lv01" created
```

## 格式化LV

格式化文件系统类型有xfs，ext4，这里测试使用ext4格式，**默认centos7下使用xfs格式，centos6为ext4格式**

```shell
[root@vm ~]# mkfs.ext4 /dev/vg01/lv01  
mke2fs 1.42.9 (28-Dec-2013)  
Filesystem label=  
OS type: Linux  
Block size=4096 (log=2)  
Fragment size=4096 (log=2)  
Stride=0 blocks, Stripe width=0 blocks  
63782912 inodes, 255126528 blocks  
12756326 blocks (5.00%) reserved for the super user  
First data block=0  
Maximum filesystem blocks=2403336192  
7786 block groups  
32768 blocks per group, 32768 fragments per group  
8192 inodes per group  
Superblock backups stored on blocks:   
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,   
        4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968,   
        102400000, 214990848  
  
Allocating group tables: done                              
Writing inode tables: done                              
Creating journal (32768 blocks): done  
Writing superblocks and filesystem accounting information: done
```

## 新建挂载点并挂载

```shell
mkdir /app  
mount /dev/vg01/lv01 /app
```

## 查看新增挂载后的块分区

```shell
[root@vm ~]# lsblk   
NAME            MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT  
vda             252:0    0    50G  0 disk   
├─vda1          252:1    0     1G  0 part /boot  
└─vda2          252:2    0    49G  0 part   
  ├─centos-root 253:0    0    44G  0 lvm  /  
  └─centos-swap 253:1    0     5G  0 lvm  [SWAP]  
vdb             252:16   0   974G  0 disk   
└─vdb1          252:17   0 973.2G  0 part   
  └─vg01-lv01   253:2    0 973.2G  0 lvm  /app

```

可以看到新增磁盘以LVM格式挂载在 `/app` 下

## 永久写入挂载点

```shell
blkid  
​  
# echo "UUID=<UUID> /app ext4 defaults 0 0" >> /etc/fstab
```

```shell

[root@docker-server ~]# blkid  
/dev/sda1: SEC_TYPE="msdos" UUID="F312-EB5D" TYPE="vfat" PARTLABEL="EFI System Partition" PARTUUID="7526c319-8c70-4fa9-ad1a-7a9a906c0f7c"  
/dev/sda2: UUID="a169778d-bcee-41d3-899e-aa68e670eb54" TYPE="xfs" PARTUUID="b9de11c9-a9a8-46c7-8484-a3f0450f648f"  
/dev/sda3: UUID="4WIoYK-aQng-dGMv-YYjq-RXaj-XKMV-o0RYCL" TYPE="LVM2_member" PARTUUID="b0ebc437-996e-4a3c-b6d3-cf5692413688"  
/dev/sdb1: UUID="PHHm7S-6haw-EeGN-2xRl-Cxna-3C9N-4QlPIy" TYPE="LVM2_member" PARTLABEL="primary" PARTUUID="68320f1b-25b0-4070-8a22-79af2af7aa69"  
/dev/mapper/centos-root: UUID="375718d4-698b-4709-b721-2c652a87c118" TYPE="xfs"  
/dev/mapper/centos-swap: UUID="d7e852bb-0ef8-4545-a3cb-4fb12b8fb9ff" TYPE="swap"  
# lvm  
/dev/mapper/vg01-lv01: UUID="37bbfff9-76da-4515-a9e5-b8820ea523e5" TYPE="ext4"   
/dev/mapper/centos-home: UUID="d3f5743c-588a-4bc1-bb6a-51775db4713e" TYPE="xfs"  
[root@docker-server ~]# cat /etc/fstab  
​  
#  
# /etc/fstab  
# Created by anaconda on Wed Jul  3 21:16:34 2024  
#  
# Accessible filesystems, by reference, are maintained under '/dev/disk'  
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info  
#  
/dev/mapper/centos-root /                       xfs     defaults        0 0  
UUID=a169778d-bcee-41d3-899e-aa68e670eb54 /boot                   xfs     defaults        0 0  
UUID=F312-EB5D          /boot/efi               vfat    umask=0077,shortname=winnt 0 0  
/dev/mapper/centos-home /home                   xfs     defaults        0 0  
/dev/mapper/centos-swap swap                    swap    defaults        0 0  
# 写入  
UUID=37bbfff9-76da-4515-a9e5-b8820ea523e5 /app ext4 defaults 0 0  
​
```

# 8、删除LVM挂载点

## 删除挂载点

```shell
[root@vm ~]# umount -v /dev/vg01/lv01  
umount: /app (/dev/mapper/vg01-lv01) unmounted
```

## 查看块设备

```shell
[root@vm ~]# lsblk   
NAME            MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT  
vda             252:0    0    50G  0 disk   
├─vda1          252:1    0     1G  0 part /boot  
└─vda2          252:2    0    49G  0 part   
  ├─centos-root 253:0    0    44G  0 lvm  /  
  └─centos-swap 253:1    0     5G  0 lvm  [SWAP]  
vdb             252:16   0   974G  0 disk   
└─vdb1          252:17   0 973.2G  0 part   
  └─vg01-lv01   253:2    0 973.2G  0 lvm
```

可以看到 `/app` 挂载点删除了，删除 /app 目录

```shell
rm -rf /app
```

## 删除LV

参数为 LV Path

```shell
[root@vm ~]# lvremove /dev/vg01/lv01  
Do you really want to remove active logical volume vg01/lv01? [y/n]: y  
  Logical volume "lv01" successfully removed
```

## 删除VG

参数为 VG Name

```shell
[root@vm ~]# vgremove vg01  
  Volume group "vg01" successfully removed
```

## 扩容VG

参数为 VG Name 和 PV Name

```shell

[root@vm ~]# vgextend centos /dev/vdb1  
  Volume group "centos" successfully extended

```

此时 PV `/dev/vdb1` 全部扩展到 VG centos下

## 查看LV

```shell
[root@vm ~]# lvdisplay   
  --- Logical volume ---  
  LV Path                /dev/centos/swap  
  LV Name                swap  
  VG Name                centos  
  LV UUID                PCx0yU-o9JZ-R92O-lI01-f8EU-DKyM-etst0H  
  LV Write Access        read/write  
  LV Creation host, time localhost.localdomain, 2018-12-24 16:46:31 +0800  
  LV Status              available  
  # open                 2  
  LV Size                5.00 GiB  
  Current LE             1280  
  Segments               1  
  Allocation             inherit  
  Read ahead sectors     auto  
  - currently set to     8192  
  Block device           253:1  
     
  --- Logical volume ---  
  LV Path                /dev/centos/root  
  LV Name                root  
  VG Name                centos  
  LV UUID                Idf9IO-AxkA-tS1C-FTgN-FsLT-d3Zk-7o1W5E  
  LV Write Access        read/write  
  LV Creation host, time localhost.localdomain, 2018-12-24 16:46:31 +0800  
  LV Status              available  
  # open                 1  
  LV Size                <44.00 GiB  
  Current LE             11263  
  Segments               1  
  Allocation             inherit  
  Read ahead sectors     auto  
  - currently set to     8192  
  Block device           253:0
```

通过查看，系统安装时选择的磁盘格式是LVM并且有两个分区，接下来扩展根分区

## 扩展LV分区

参数为 LV Path

```shell
[root@vm ~]# lvextend -l +100%FREE /dev/centos/root  
  Size of logical volume centos/root changed from <44.00 GiB (11263 extents) to <1017.23 GiB (260410 extents).  
  Logical volume centos/root successfully resized
```

如果发现误操作，需要还原，可以通过以下命令缩小LV分区

```shell
lvreduce -L 1017.2G  /dev/centos/root
```

## 加载扩容到系统

此时查看系统可以看到已经扩容，但没有加载到文件系统

```shell
[root@vm ~]# lsblk   
NAME            MAJ:MIN RM    SIZE RO TYPE MOUNTPOINT  
vda             252:0    0     50G  0 disk   
├─vda1          252:1    0      1G  0 part /boot  
└─vda2          252:2    0     49G  0 part   
  ├─centos-root 253:0    0 1017.2G  0 lvm  /  
  └─centos-swap 253:1    0      5G  0 lvm  [SWAP]  
vdb             252:16   0    974G  0 disk   
└─vdb1          252:17   0  973.2G  0 part   
  └─centos-root 253:0    0 1017.2G  0 lvm  /  
[root@vm ~]# df -h  
Filesystem               Size  Used Avail Use% Mounted on  
/dev/mapper/centos-root   44G  3.7G   41G   9% /  
devtmpfs                 7.8G     0  7.8G   0% /dev  
tmpfs                    7.8G     0  7.8G   0% /dev/shm  
tmpfs                    7.8G  777M  7.0G  10% /run  
tmpfs                    7.8G     0  7.8G   0% /sys/fs/cgroup  
/dev/vda1               1014M  179M  836M  18% /boot  
tmpfs                    1.6G   36K  1.6G   1% /run/user/0  
tmpfs                    1.6G   40K  1.6G   1% /run/user/1001

```

**加载扩容到系统** 参数为 LV path，**被扩容的挂载点一定是LVM格式**

```shell
[root@vm ~]# xfs_growfs /dev/centos/root  
meta-data=/dev/mapper/centos-root isize=512    agcount=4, agsize=2883328 blks  
         =                       sectsz=512   attr=2, projid32bit=1  
         =                       crc=1        finobt=0 spinodes=0  
data     =                       bsize=4096   blocks=11533312, imaxpct=25  
         =                       sunit=0      swidth=0 blks  
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1  
log      =internal               bsize=4096   blocks=5631, version=2  
         =                       sectsz=512   sunit=0 blks, lazy-count=1  
realtime =none                   extsz=4096   blocks=0, rtextents=0  
data blocks changed from 11533312 to 266659840
```

说明：若使用ext4文件格式（centos6），是使用resize2fs命令来生效 **查看磁盘**

```shell
[root@vm ~]# df -h  
Filesystem               Size  Used Avail Use% Mounted on  
/dev/mapper/centos-root 1018G  3.7G 1014G   1% /  
devtmpfs                 7.8G     0  7.8G   0% /dev  
tmpfs                    7.8G     0  7.8G   0% /dev/shm  
tmpfs                    7.8G  777M  7.0G  10% /run  
tmpfs                    7.8G     0  7.8G   0% /sys/fs/cgroup  
/dev/vda1               1014M  179M  836M  18% /boot  
tmpfs                    1.6G   36K  1.6G   1% /run/user/0  
tmpfs                    1.6G   40K  1.6G   1% /run/user/1001
```

#### **情况 2：文件系统是 ext4**

如果文件系统是 ext4，则需要使用 `resize2fs` 工具扩展文件系统。

运行以下命令：

```
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
```

完成后，再次运行 `df -h` 检查文件系统的大小是否已更新。
