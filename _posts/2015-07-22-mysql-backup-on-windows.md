---
layout: post
title: Windows 下 MySQL 简单定时自动备份、删除过期备份
date: 2015-07-22 01:08
author: admin
comments: true
categories: [MySQL]
tags: [MySQL]
---

## 问题

MySQL Workbench 客户端虽然好用，但并不提供自动备份功能。手工备份，确实繁琐。

<!-- more -->

## 环境

- Windows Server 2012
- MySQL 5.6.24

## 思考

MySQL 提供了 mysqldump 来进行备份。那么我们可否使用该工具，结合Windows 的定时任务功能，来实现 MySQL 定时自动备份呢？

## 解决

新建一个 数据库备份文件存放目录，本例为`D:\db_backup`.新建一个批处理文件，可以起任意名，本例为 `mysql_backup_tool.bat` ，文件内容如下：

	rem auther:www.waylau.com
	rem date:20150724
	rem ******MySQL backup start********
	@echo off
	
	forfiles /p "D:\db_backup" /m emsc_backup_*.sql -d -30 /c "cmd /c del /f @path"
	
	set "Ymd=%date:~0,4%%date:~5,2%%date:~8,2%0%time:~1,1%%time:~3,2%%time:~6,2%"
	C:\mysql\bin\mysqldump --opt --single-transaction=TRUE   --user=root  --password=123456 --host=127.0.0.1 --protocol=tcp --port=3306 --default-character-set=utf8 --single-transaction=TRUE --routines --events "emsc" > D:\db_backup\emsc_backup_%Ymd%.sql
	
	@echo on
	rem ******MySQL backup end********

其中,`forfiles`删除过期文件的命令，`-d -30`删除30天的文件。`C:\mysql\bin\mysqldump` 为 MySQL 安装时，`mysqldump.exe` 文件所在路径，`--user=root` 指 MySQL 用户名为 `root`  `--password=123456` 指 MySQL 密码为`123456`,`"emsc"` 为要备份的数据库的名称,`emsc_backup_%Ymd%.sql`， 为备份文件的名称，这个名称是根据当前的时间规则生成的，比如今天生产的备份文件，名称为`emsc_backup_20150724012120.sql` 。

### 定期任务

一般的，我们的备份周期是1天，备份的存储时间是30天，30天后的备份文件将被删除。备份的时间应选择在系统运行负荷最小的时间，比如凌晨1点到3点。具体步骤如下：
在“服务器管理”--“工具”中选择“任务计划程序”
 
选择“创建基本任务”

![](http://99btgc01.info/uploads/2015/07/001.png)

任务的名称起为“MySQL backup” ，点击“下一步”

![](http://99btgc01.info/uploads/2015/07/002.png)

选择“每天”，点击“下一天”

![](http://99btgc01.info/uploads/2015/07/003.png)

设置开始备份的时间，按实际的需要选择一个服务器负荷最小的时间，点击“下一步”

![](http://99btgc01.info/uploads/2015/07/004.png)

![](http://99btgc01.info/uploads/2015/07/005.png)

选择要启动的程序，这里我们选择 mysql_backup_tool.bat 文件

![](http://99btgc01.info/uploads/2015/07/006.png)

![](http://99btgc01.info/uploads/2015/07/007.png)

![](http://99btgc01.info/uploads/2015/07/008.png)

![](http://99btgc01.info/uploads/2015/07/009.png)

点击“完成”后，就能在计划任务列表中看到“MySQL backup”。

![](http://99btgc01.info/uploads/2015/07/010.png)


## 参考
- <http://dev.mysql.com/doc/mysql-backup-excerpt/5.7/en/backup-and-recovery.html>


