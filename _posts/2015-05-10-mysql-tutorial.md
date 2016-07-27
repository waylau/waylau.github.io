---
layout: post
title: MySQL 使用教程
date: 2015-05-10 02:41
author: admin
comments: true
categories: [MySQL]
tags: [MySQL,tutorial]
---

## 关于 MySQL

MySQL 是最流行的开源数据库。

本文简明的讲解了 MySQL 如何下载安装到使用的整个过程。

<!-- more -->

MySQL 支持多种特性：

* 使用 C和 C++编写，并使用了多种编译器进行测试，保证了源代码的可移植性。
* 支持 AIX、FreeBSD、HP-UX、Linux、Mac OS、NovellNetware、OpenBSD、OS/2 Wrap、Solaris、Windows等多种操作系统。
* 为多种编程语言提供了 API。这些编程语言包括 C、C++、Python、Java、Perl、PHP、Eiffel、Ruby,.NET和 Tcl 等。支持多线程，充分利用 CPU 资源。
* 优化的 SQL查询算法，有效地提高查询速度。
* 既能够作为一个单独的应用程序应用在客户端服务器网络环境中，也能够作为一个库而嵌入到其他的软件中。
* 提供多语言支持，常见的编码如中文的 GB 2312、BIG5，日文的 Shift_JIS等都可以用作数据表名和数据列名。
* 提供 TCP/IP、ODBC 和 JDBC等多种数据库连接途径。
* 提供用于管理、检查、优化数据库操作的管理工具。
* 支持大型的数据库。可以处理拥有上千万条记录的大型数据库。
* 支持多种存储引擎。
* MySQL 是开源的，所以你不需要支付额外的费用。
* MySQL 使用标准的 SQL数据语言形式。
* MySQL 对 PHP 有很好的支持，PHP是目前最流行的 Web 开发语言。
* MySQL是可以定制的，采用了 GPL协议，你可以修改源码来开发自己的 MySQL 系统。
* 在线 DDL/更改功能，数据架构支持动态应用程序和开发人员灵活性（5.6[3] 新增）
* 复制全局事务标识，可支持自我修复式集群（5.6[3] 新增）
* 复制无崩溃从机，可提高可用性（5.6[3] 新增）
* 复制多线程从机，可提高性能（5.6[3] 新增）


## 下载、安装

下载地址：<http://www.mysql.com/downloads/>

本例用的是 MySQL Community Edition (GPL) 版本，开源免费。

* 版本号：MySQL Community Server 5.6.24
* 平台类型： Windows

安装方式，可以选择 MySQL Installer MSI 或者是 编译压缩包（形如 mysql-5.6.26-win32.zip 或 mysql-5.6.26-winx64.zip）。

如果是 MySQL Installer MSI 方式安装，请参阅 <http://www.waylau.com/mysql-5-6-13-windows-platform-installation-use-graphic/>。本教程采用  编译压缩包 的安装形式。

下载完成后，解压到放到任意目录，本例为  `C:\mysql` 目录

## 设置环境变量

放到 Path 变量下增加 ，C:\mysql\bin

![](http://99btgc01.info/uploads/2015/05/001%285%29.jpg)
 
## 配置 

复制安装目录下的 my-default.ini 并重命名为 my.ini ，修改为 

    [mysqld]
    
    basedir=C:/mysql
    datadir=D:/MysqlData/data
     
basedir 为 mysql 安装目录。datadir 为数据存放目录

## 安装服务

安装作为 Windows　服务。

MySQL 会随着 Windows　的启动和关闭而启动和关闭 

mysqld --install

    C:\Users\Administrator.USER-20150424OX>mysqld –install
    Service successfully installed.
 

其他可选的有：设置为手动启动或者关闭服务

mysqld --install-manual

移除服务

mysqld --remove



## 启动、关闭服务

启动

NET START MySQL
 
    C:\Users\Administrator.USER-20150424OX>NET START MySQL
    MySQL 服务正在启动 .
    MySQL 服务已经启动成功。

关闭

NET STOP MySQL

    C:\Users\Administrator.USER-20150424OX>NET STOP MySQL
    MySQL 服务正在停止.
    MySQL 服务已成功停止。

##	测试安装是否成功

mysqlshow

    C:\Users\Administrator.USER-20150424OX>mysqlshow
    +--------------------+
    |     Databases      |
    +--------------------+
    | information_schema |
    | test               |
    +--------------------+

mysqlshow -u root mysql
    
    C:\Users\Administrator.USER-20150424OX>mysqlshow -u root mysql
    Database: mysql
    +---------------------------+
    |          Tables           |
    +---------------------------+
    | columns_priv              |
    | db                        |
    | event                     |
    | func                      |
    | general_log               |
    | help_category             |
    | help_keyword              |
    | help_relation             |
    | help_topic                |
    | innodb_index_stats        |
    | innodb_table_stats        |
    | ndb_binlog_index          |
    | plugin                    |
    | proc                      |
    | procs_priv                |
    | proxies_priv              |
    | servers                   |
    | slave_master_info         |
    | slave_relay_log_info      |
    | slave_worker_info         |
    | slow_log                  |
    | tables_priv               |
    | time_zone                 |
    | time_zone_leap_second     |
    | time_zone_name            |
    | time_zone_transition      |
    | time_zone_transition_type |
    | user                      |
    +---------------------------+

mysqladmin version status proc

    
    C:\Users\Administrator.USER-20150424OX>mysqladmin version status proc
    mysqladmin  Ver 8.42 Distrib 5.6.24, for Win64 on x86_64
    Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Server version          5.6.24
    Protocol version        10
    Connection              localhost via TCP/IP
    TCP port                3306
    Uptime:                 9 min 12 sec
    
    Threads: 1  Questions: 7  Slow queries: 0  Opens: 67  Flush tables: 1  Open tabl
    es: 60  Queries per second avg: 0.012
    Uptime: 552  Threads: 1  Questions: 8  Slow queries: 0  Opens: 67  Flush tables:
     1  Open tables: 60  Queries per second avg: 0.014
    mysqladmin: process list failed; error: 'Access denied; you need (at least one o
    f) the PROCESS privilege(s) for this operation'

mysql test

    C:\Users\Administrator.USER-20150424OX>mysql test
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 4
    Server version: 5.6.24 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
## 使用

列出所有的帮助命令

mysql --help


连接到服务器，其中 host 是服务器IP, user 是用户名

mysql -h host -u user -p

如果服务器在本地 ，

mysql -u user –p

提示输入密码，验证通过后，如下

    C:\Users\Administrator.USER-20150424OX>mysql -u root -p
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 6
    Server version: 5.6.24 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>

断开与服务器的链接

QUIT

    mysql> QUIT
    Bye
    
查询（命令语句是不区分大小写）  
    
    mysql> SELECT VERSION(), CURRENT_DATE;
    +-----------+--------------+
    | VERSION() | CURRENT_DATE |
    +-----------+--------------+
    | 5.6.24    | 2015-05-10   |
    +-----------+--------------+
    1 row in set (0.01 sec)

列出所有数据库
    
    mysql> SHOW DATABASES;
    +--------------------+
    | Database           |
    +--------------------+
    | information_schema |
    | mysql              |
    | performance_schema |
    | test               |
    +--------------------+
    4 rows in set (0.00 sec)

创建一个自己的数据库 menagerie

    mysql> CREATE DATABASE menagerie;
    Query OK, 1 row affected (0.03 sec)


给自己授予管理员权限

    GRANT ALL ON menagerie.* TO 'your_mysql_name'@'your_client_host';
    
访问数据库 menagerie

    mysql> USE menagerie
    Database changed

列出数据库表

    mysql> SHOW TABLES;
    Empty set (0.00 sec)

创建表 pet

    mysql> CREATE TABLE pet (name VARCHAR(20), owner VARCHAR(20),
        ->  species VARCHAR(20), sex CHAR(1), birth DATE, death DATE);
    Query OK, 0 rows affected (0.43 sec)

查看 pet 表的详情

    mysql> DESCRIBE pet;
    +---------+-------------+------+-----+---------+-------+
    | Field   | Type        | Null | Key | Default | Extra |
    +---------+-------------+------+-----+---------+-------+
    | name    | varchar(20) | YES  |     | NULL    |       |
    | owner   | varchar(20) | YES  |     | NULL    |       |
    | species | varchar(20) | YES  |     | NULL    |       |
    | sex     | char(1)     | YES  |     | NULL    |       |
    | birth   | date        | YES  |     | NULL    |       |
    | death   | date        | YES  |     | NULL    |       |
    +---------+-------------+------+-----+---------+-------+
    6 rows in set (0.01 sec)

插入数据

    mysql> INSERT INTO pet
        -> VALUES ('Puffball','Diane','hamster','f','1999-03-30',NULL);
    Query OK, 1 row affected (0.04 sec)

查询所有的数据

    mysql> SELECT * FROM pet;
    +----------+-------+---------+------+------------+-------+
    | name     | owner | species | sex  | birth      | death |
    +----------+-------+---------+------+------------+-------+
    | Puffball | Diane | hamster | f    | 1999-03-30 | NULL  |
    +----------+-------+---------+------+------------+-------+
    1 row in set (0.00 sec)

修改数据

    mysql> UPDATE pet SET birth = '1989-08-31' WHERE name = 'Puffball';
    Query OK, 1 row affected (0.11 sec)
    Rows matched: 1  Changed: 1  Warnings: 0

删除数据

    mysql> DELETE FROM pet;
    Query OK, 1 row affected (0.04 sec)


## 参考

* <http://dev.mysql.com/doc/refman/5.6/en/index.html>