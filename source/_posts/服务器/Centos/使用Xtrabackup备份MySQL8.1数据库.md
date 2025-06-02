---
title: 使用Xtrabackup备份MySQL8.1数据库
categories:
  - 服务器
tags:
  - Centos
  - 食用教程
  - MySQL
  - Xtrabackup
abbrlink: 54b7
date: 2025-03-27 14:15:00
---

## 部署

```bash
# 下载二进制的 Xtrabackup 压缩包，开箱即用：
## 环发内网地址
wget http://192.168.1.116:40002/huanfa/devTools/percona-xtrabackup-8.1.0-1-Linux-x86_64.glibc2.17.tar.gz


# 解压缩
tar -zxvf percona-xtrabackup-8.1.0-1-Linux-x86_64.glibc2.17.tar.gz

# 移动目录
mv percona-xtrabackup-8.1.0-1-Linux-x86_64.glibc2.17 /app/xtrabackup

# 配置软链接
ln -sf /app/xtrabackup/bin/* /usr/bin/

# 查看版本命令：“xtrabackup --version”，输出版本号表示安装成功：
xtrabackup --version

xtrabackup version 8.0.34-29 based on MySQL server 8.0.34 Linux (x86_64) (revision id: 5ba706ee)
```

## MySQL备份需要的权限

> 1、Reload：用于执行 FLUSH TABLES WITH REDO LOCK 和 FLUSH NO_WRITE_TO_BINLOG TABLES 是必需权限。  
> 2、Replication client：用于执行 SHOW MASTER STATUS 和 SHOW SLAVE STATUS 查看位点信息，是必需权限。  
> 3、BACKUP_ADMIN：用于执行 LOCK INSTANCE FOR BACKUP，是必需权限。  
> 4、Process：用于执行 SHOW ENGINE INNODB STATUS 和 SHOW PROCESSLIST 是必需权限。  
> 5、SYSTEM_VARIABLES_ADMIN：用于在增量备份时执行 SET GLOBAL mysqlbackup.backupid = xxx 操作，是非必需权限。  
> 6、SUPER：在指定 --kill-long-queries-timeout 需要杀掉慢查询，和从库备份指定 --safe-slave-backup 需要重启复制，需要用到该权限。  
> 7、SHOW VIEW：确认是否有非 INNODB 引擎表。  
> 8、如果使用 Page Tracking 进行增量备份，还需要 mysql.component 表的查询权限。  
> 9、如果指定 --history 还需要 performance_schema.xtraback_history 的 SELECT、INSERT、CREATE、ALTER 权限。

```ini
# 修改my.cnf文件
gtid_mode=ON
enforce_gtid_consistency=ON
```

## MySQL8.1需要使用非root用户

```sql
CREATE USER 'bkpuser'@'localhost' IDENTIFIED BY 's3cr%T';

GRANT BACKUP_ADMIN, PROCESS, RELOAD, LOCK TABLES, REPLICATION CLIENT ON *.* TO 'bkpuser'@'localhost';

GRANT SELECT ON performance_schema.log_status TO 'bkpuser'@'localhost';

GRANT SELECT ON performance_schema.keyring_component_status TO bkpuser@'localhost';

GRANT SELECT ON performance_schema.replication_group_members TO bkpuser@'localhost';
```

‍

## 全量备份

```bash
# 运行之前先保证备份目录是否存在

xtrabackup --backup --slave-info\
 -u bkpuser\
 -H 127.0.0.1\
 -P3306\
 -p's3cr%T'\
 --compress\
 --parallel=5\
 --target-dir=/app/backup/bakup_`date +"%F_%H_%M_%S"`


# 如果需要压缩备份 在后面加上 
 | gzip - > /app/backup/bakup_`date +"%F_%H_%M_%S"`.gz
```

## 
<br/>


## <span data-type="text" style="white-space-collapse: break-spaces;">~~~bash</span>
