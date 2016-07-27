---
layout: post
title: RHEL\CentOS 7 下 MySQL 连接数被限制为214个
date: 2015-07-09 01:08
author: admin
comments: true
categories: [CentOS,MySQL]
tags: [CentOS,MySQL]
---

## 问题

项目中，由于连接数过多，提示“Too many connections”，需要增加连接数。我在 `/etc/my.cnf`中修改了
	
	max_connections = 2000

但是， 实际连接数一直被限制在 214 

	mysql> show variables like "max_connections";
	+-----------------+-------+
	| Variable_name   | Value |
	+-----------------+-------+
	| max_connections | 214   |
	+-----------------+-------+
	1 row in set

MySQL max_connections 总是 214 。不能设大了？

<!-- more -->

## 环境

- CentOS 7.1
- MySQL 5.6.25

## 思考

如果我设置连接小于214时，比如 200，那么实际连接数就是 200，也就是说，我的配置文件是没有问题的。

查 MySQL 官方文档，里面说了

> The maximum number of connections MySQL can support depends on the quality of the thread library on a given platform, the amount of RAM available, how much RAM is used for each connection, the workload from each connection, and the desired response time. Linux or Solaris should be able to support at 500 to 1000 simultaneous connections routinely and as many as 10,000 connections if you have many gigabytes of RAM available and the workload from each is low or the response time target undemanding. Windows is limited to (open tables × 2 + open connections) < 2048 due to the Posix compatibility layer used on that platform.
	
>Increasing open-files-limit may be necessary. Also see Section 2.5, “Installing MySQL on Linux”, for how to raise the operating system limit on how many handles can be used by MySQL.


大概意思是 MySQL 能够支持的最大连接数量受限于操作系统,必要时可以增大 [open-files-limit](http://dev.mysql.com/doc/refman/5.7/en/server-options.html#option_mysqld_open-files-limit)。换言之，连接数与文件打开数有关。

## 解决

执行 

	[root@emsc ~]# ulimit -n
	1024

可知，操作系统最大文件描述符限制为 1024，
在 配置文件中添加 
	
	open_files_limit = 65535

实际上也没有生效

更改 MySQL 在 Linux 的最大文件描述符限制，编辑 `/usr/lib/systemd/system/mysqld.service` 文件，在文件最后添加:

	LimitNOFILE=65535
	LimitNPROC=65535

保存后，执行下面命令，使配置生效

	$ systemctl daemon-reload
	$ systemctl restart  mysqld.service

实际连接数到 2000 了，解决

	mysql> show variables like "max_connections";
	+-----------------+-------+
	| Variable_name   | Value |
	+-----------------+-------+
	| max_connections | 2000  |
	+-----------------+-------+
	1 row in set


## 参考
- <http://dev.mysql.com/doc/refman/5.7/en/too-many-connections.html>
- <http://www.oschina.net/question/853151_241231>

