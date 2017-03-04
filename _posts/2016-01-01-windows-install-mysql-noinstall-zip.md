---
layout: post
title: 在 Windows 上使用 noinstall Zip 文件安装 MySQL
date: 2016-01-01 01:08
author: admin
comments: true
categories: [MySQL]
tags: [MySQL,安装]
---

在 Windows 环境下，最简单的安装 MySQL 方式自然是使用  MySQL Installer 进行“傻瓜”式点下一步安装即可，可以参见[mysql-5.6.13在windows平台下的安装、使用（图解）](http://www.waylau.com/mysql-5-6-13-windows-platform-installation-use-graphic/)。本文介绍了另外一种使用 noinstall Zip 文件（免安装版）安装 MySQL。

<!-- more -->

## 下载安装包

地址： <http://dev.mysql.com/downloads/mysql/>

本例使用 MySQL 版本为：Windows (x86, 64-bit), ZIP Archive	5.7.10	(mysql-5.7.10-winx64.zip)

本例使用的操作系统为：Win7 Sp1 x64

## 解压到安装目录

1. 确保使用管理员权限的用户登陆操作系统
2. 选择安装位置。传统上，MySQL server 安装在 `C:\mysql`。 MySQL 的安装向导（MySQL Installer）安装 MySQL 在`C:\Program Files\MySQL`。如果您没有在 `C:\mysql` 安装 MySQL，你必须在启动或者在  option file 文件中指定的安装目录路径。本例安装在`C:\Program Files\MySQL`目录。
3. 将安装包解压后放到安装目录下，如下：

![](http://a.hiphotos.baidu.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=f40f5365d188d43ff4a991f74d25a326/9345d688d43f8794accebc3ed51b0ef41bd53ae8.jpg?referer=a368c26f7ff0f73681e9793186a6&x=.jpg)


## 创建配置文件

配置文件是用于设置 MySQL 的配置，解压包下有一个 my-default.ini 文件，就是模版。复制该模版从命名为 my.ini，则 MySQL 就会从 my.ini 里读取配置。

my.ini 放在 `C:\Windows`目录。

本例安装目录和数据设置在 (`C:\Program Files\MySQL\mysql-5.7.10-winx64` 和 `D:\mysqlData\data`)，配置如下：

```
[mysqld]
# set basedir to your installation path
basedir = C:/Program Files/MySQL/mysql-5.7.10-winx64
# set datadir to the location of your data directory
datadir = D:/mysqlData/data
```

MySQL 5.7.6 之后，noinstall 安装包并没有提供 data 目录，所以要初始化一个 data 目录

## 添加 MySQL 工具到 PATH

为了方便使用  MySQL 工具，添加 MySQL bin 目录添加到 PATH 是个不错的选择。

本例将 `C:\Program Files\MySQL\mysql-5.7.10-winx64\bin`添加到 PATH,如下：

![](http://a.hiphotos.baidu.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=92f21ca8d33f8794d7ff482be2207fc9/d788d43f8794a4c27da9183b09f41bd5ad6e3913.jpg?referer=03f8b2d0f036afc3571b0b5587f1&x=.jpg)

## 初始化 data 目录


使用命令行

```
mysqld --defaults-file=C:\Windows\my.ini --initialize --console
```

其中 `--defaults-file` 指向了我们之前创建的 my.ini 文件。

控制台输出如下：

```
C:\Users\Administrator>mysqld --defaults-file=C:\Windows\my.ini --initialize --c
onsole
2016-01-03T04:38:27.634571Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is
 deprecated. Please use --explicit_defaults_for_timestamp server option (see doc
umentation for more details).
2016-01-03T04:38:27.634571Z 0 [Warning] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'E
RROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will
be merged with strict mode in a future release.
2016-01-03T04:38:27.634571Z 0 [Warning] 'NO_AUTO_CREATE_USER' sql mode was not s
et.
2016-01-03T04:38:29.196573Z 0 [Warning] InnoDB: New log files created, LSN=45790

2016-01-03T04:38:29.576574Z 0 [Warning] InnoDB: Creating foreign key constraint
system tables.
2016-01-03T04:38:29.696574Z 0 [Warning] No existing UUID has been found, so we a
ssume that this is the first time that this server has been started. Generating
a new UUID: d657df18-b1d3-11e5-b812-00ac86a5db41.
2016-01-03T04:38:29.726574Z 0 [Warning] Gtid table is not ready to be used. Tabl
e 'mysql.gtid_executed' cannot be opened.
2016-01-03T04:38:29.736574Z 1 [Note] A temporary password is generated for root@
localhost: m1>X:,<-2Gfl
```

若无异常，则会在 `D:\mysqlData`下创建一个 data 文件夹。
其中，最后那个 `m1>X:,<-2Gfl` 就是 root 用户的随机生产的密码

## 启动、关闭  MySQL server

执行`mysqld`就能启动 MySQL server，或者执行 `mysqld --console`可以看到完整的启动信息：

```
C:\Users\Administrator>mysqld --console
2016-01-03T03:39:47.313272Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is
 deprecated. Please use --explicit_defaults_for_timestamp server option (see doc
umentation for more details).
2016-01-03T03:39:47.313272Z 0 [Warning] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'E
RROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will
be merged with strict mode in a future release.
2016-01-03T03:39:47.313272Z 0 [Warning] 'NO_AUTO_CREATE_USER' sql mode was not s
et.
2016-01-03T03:39:47.313272Z 0 [Warning] Insecure configuration for --secure-file
-priv: Current value does not restrict location of generated files. Consider set
ting it to a valid, non-empty path.
2016-01-03T03:39:47.313272Z 0 [Note] mysqld (mysqld 5.7.10) starting as process
1420 ...
2016-01-03T03:39:47.313272Z 0 [Note] InnoDB: Mutexes and rw_locks use Windows in
terlocked functions
2016-01-03T03:39:47.313272Z 0 [Note] InnoDB: Uses event mutexes
2016-01-03T03:39:47.313272Z 0 [Note] InnoDB: _mm_lfence() and _mm_sfence() are u
sed for memory barrier
2016-01-03T03:39:47.313272Z 0 [Note] InnoDB: Compressed tables use zlib 1.2.3
2016-01-03T03:39:47.313272Z 0 [Note] InnoDB: Number of pools: 1
2016-01-03T03:39:47.313272Z 0 [Note] InnoDB: Not using CPU crc32 instructions
2016-01-03T03:39:47.328872Z 0 [Note] InnoDB: Initializing buffer pool, total siz
e = 128M, instances = 1, chunk size = 128M
2016-01-03T03:39:47.344472Z 0 [Note] InnoDB: Completed initialization of buffer
pool
2016-01-03T03:39:47.453673Z 0 [Note] InnoDB: Highest supported file format is Ba
rracuda.
2016-01-03T03:39:47.469273Z 0 [Note] InnoDB: Log scan progressed past the checkp
oint lsn 2492338
2016-01-03T03:39:47.469273Z 0 [Note] InnoDB: Doing recovery: scanned up to log s
equence number 2492347
2016-01-03T03:39:47.469273Z 0 [Note] InnoDB: Doing recovery: scanned up to log s
equence number 2492347
2016-01-03T03:39:47.469273Z 0 [Note] InnoDB: Database was not shutdown normally!

2016-01-03T03:39:47.469273Z 0 [Note] InnoDB: Starting crash recovery.
2016-01-03T03:39:48.186874Z 0 [Note] InnoDB: Removed temporary tablespace data f
ile: "ibtmp1"
2016-01-03T03:39:48.186874Z 0 [Note] InnoDB: Creating shared tablespace for temp
orary tables
2016-01-03T03:39:48.186874Z 0 [Note] InnoDB: Setting file '.\ibtmp1' size to 12
MB. Physically writing the file full; Please wait ...
2016-01-03T03:39:48.389674Z 0 [Note] InnoDB: File '.\ibtmp1' size is now 12 MB.
2016-01-03T03:39:48.389674Z 0 [Note] InnoDB: 96 redo rollback segment(s) found.
96 redo rollback segment(s) are active.
2016-01-03T03:39:48.389674Z 0 [Note] InnoDB: 32 non-redo rollback segment(s) are
 active.
2016-01-03T03:39:48.389674Z 0 [Note] InnoDB: Waiting for purge to start
2016-01-03T03:39:48.452074Z 0 [Note] InnoDB: 5.7.10 started; log sequence number
 2492347
2016-01-03T03:39:48.452074Z 0 [Note] Plugin 'FEDERATED' is disabled.
2016-01-03T03:39:48.452074Z 0 [Note] InnoDB: Loading buffer pool(s) from D:\mysq
lData\data\ib_buffer_pool
2016-01-03T03:39:48.452074Z 0 [Note] InnoDB: not started
2016-01-03T03:39:48.623675Z 0 [Warning] Failed to set up SSL because of the foll
owing SSL library error: SSL context is not usable without certificate and priva
te key
2016-01-03T03:39:48.623675Z 0 [Note] Server hostname (bind-address): '*'; port:
3306
2016-01-03T03:39:48.623675Z 0 [Note] IPv6 is available.
2016-01-03T03:39:48.623675Z 0 [Note]   - '::' resolves to '::';
2016-01-03T03:39:48.623675Z 0 [Note] Server socket created on IP: '::'.
2016-01-03T03:39:48.810875Z 0 [Note] Event Scheduler: Loaded 0 events
2016-01-03T03:39:48.810875Z 0 [Note] mysqld: ready for connections.
Version: '5.7.10'  socket: ''  port: 3306  MySQL Community Server (GPL)
2016-01-03T03:39:48.904475Z 0 [Note] InnoDB: Buffer pool(s) load completed at 16
0103 11:39:48
```

关闭执行 `mysqladmin -u root shutdown`

### 作为 Windows Service 安装

作为 Windows Service 可以随着操作系统自启动。安装前，先关闭 MySQL server，关闭执行 `mysqladmin -u root shutdown`，
再执行 `mysqld --install`即可。

若要卸载该服务器，执行`mysqld --remove`

### 使用 NET 工具启动、关闭 MySQL

将 MySQL server 作为 Windows Service 安装之后，可以使用 NET 工具方便启动、关闭 MySQL（指令不区分大小写）。

启动执行：

```
C:\Users\Administrator>NET START MySQL
MySQL 服务正在启动 .
MySQL 服务已经启动成功。
```

关闭执行：

```
C:\Users\Administrator>NET STOP MySQL
MySQL 服务正在停止.
MySQL 服务已成功停止。
```

## 加密默认用户账号

### 初始化 root 用户

在 MySQL server启动后，用 root 用户登陆

```
shell> mysql -u root -p
Enter password: (enter the random root password here)
```

执行修改密码的指令：

```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';
```


## 问题

### 问题1

执行`mysqld --defaults-file=C:\Windows\my.ini --initialize`时，出现
下面称为：

```
C:\Users\Administrator>mysqld --defaults-file=C:\Windows\my.ini --initialize
mysqld: Can't create directory 'D:\mysqlData\data\' (Errcode: 2 - No such file o
r directory)
2016-01-01T15:08:10.531737Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is
 deprecated. Please use --explicit_defaults_for_timestamp server option (see doc
umentation for more details).
2016-01-01T15:08:10.531737Z 0 [Warning] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'E
RROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will
be merged with strict mode in a future release.
2016-01-01T15:08:10.531737Z 0 [Warning] 'NO_AUTO_CREATE_USER' sql mode was not s
et.
2016-01-01T15:08:10.534737Z 0 [ERROR] Aborting
```

解决方法：指定的目录不存在，先创建`D:\mysqlData`目录，再执行上述命令


## 参考

* <http://dev.mysql.com/doc/refman/5.7/en/windows-install-archive.html>
* <http://www.waylau.com/mysql-5-6-13-windows-platform-installation-use-graphic/>
* <http://dev.mysql.com/doc/refman/5.7/en/data-directory-initialization-mysqld.html>