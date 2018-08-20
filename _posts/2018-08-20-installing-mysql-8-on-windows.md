---
layout: post
title: MySQL 8 在 Windows 下安装及使用    
date: 2018-08-20 00:22
author: admin
comments: true
categories: [MySQL]
tags: [MySQL]
---

MySQL 8 带来了全新的体验，比如支持 NoSQL、JSON 等，拥有比 MySQL 5.7 两倍以上的性能提升。本文讲解如何在 Windows 下安装 MySQL 8，以及基本的 MySQL 用法。

<!-- more -->


## 下载

下载地址 <https://dev.mysql.com/downloads/mysql/8.0.html>。

本例为：MySQL Community Server 8.0.12。

## 解压

解压至安装目录，比如 D 盘根目录下。

本例为：`D:\mysql-8.0.12-winx64`。

## 创建 my.ini 

my.ini 是 MySQL 安装的配置文件：

```
[mysqld]
# 安装目录
basedir=D:\\mysql-8.0.12-winx64
# 数据存放目录
datadir=D:\\mysqlData\\data
```


my.ini放置在 MySQL 安装目录的根目录下。需要注意的是，要先创建`D:\mysqlData`目录。`data`目录是由 MySQL 来创建。


## 初始化安装

执行：

```
mysqld --defaults-file=D:\mysql-8.0.12-winx64\my.ini --initialize --console
```

控制台输出如下，说明安装成功：

```
>mysqld --defaults-file=D:\mysql-8.0.12-winx64\my.ini --initialize --console
2018-08-20T16:14:45.287448Z 0 [System] [MY-013169] [Server] D:\mysql-8.0.12-winx64\bin\mysqld.exe (mysqld 8.0.12) initializing of server in progress as process 5012
2018-08-20T16:14:45.289628Z 0 [ERROR] [MY-010457] [Server] --initialize specified but the data directory has files in it. Aborting.
2018-08-20T16:14:45.299329Z 0 [ERROR] [MY-010119] [Server] Aborting
2018-08-20T16:14:45.301316Z 0 [System] [MY-010910] [Server] D:\mysql-8.0.12-winx64\bin\mysqld.exe: Shutdown complete (mysqld 8.0.12)  MySQL Community Server - GPL.

D:\mysql-8.0.12-winx64\bin>mysqld --defaults-file=D:\mysql-8.0.12-winx64\my.ini --initialize --console
2018-08-20T16:15:25.729771Z 0 [System] [MY-013169] [Server] D:\mysql-8.0.12-winx64\bin\mysqld.exe (mysqld 8.0.12) initializing of server in progress as process 18148
2018-08-20T16:15:43.569562Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: L-hk!rBuk9-.
2018-08-20T16:15:55.811470Z 0 [System] [MY-013170] [Server] D:\mysql-8.0.12-winx64\bin\mysqld.exe (mysqld 8.0.12) initializing of server has completed
```


其中，“L-hk!rBuk9-.”就是 root 用户的初始化密码。稍后可以做更改。

## 启动、关闭 MySQL server

执行mysqld就能启动 MySQL server，或者执行 mysqld --console可以看到完整的启动信息：

```
>mysqld --console
2018-08-20T16:18:23.698153Z 0 [Warning] [MY-010915] [Server] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'ERROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will be merged with strict mode in a future release.
2018-08-20T16:18:23.698248Z 0 [System] [MY-010116] [Server] D:\mysql-8.0.12-winx64\bin\mysqld.exe (mysqld 8.0.12) starting as process 16304
2018-08-20T16:18:27.624422Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2018-08-20T16:18:27.793310Z 0 [System] [MY-010931] [Server] D:\mysql-8.0.12-winx64\bin\mysqld.exe: ready for connections. Version: '8.0.12'  socket: ''  port: 3306  MySQL Community Server - GPL.
```


关闭，可以执行 `mysqladmin -u root shutdown`。


## 使用 MySQL 客户端

使用 mysql 来登录，账号为 root，密码为“L-hk!rBuk9-.”：

```
>mysql -u root -p
Enter password: ************
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.12

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

执行下面的语句来改密码。其中“123456”即为新密码。

```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
Query OK, 0 rows affected (0.13 sec)
```

## MySQL 常用指令

### 显示已有的数据库：

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.08 sec)
```

### 创建新的数据库：

```
mysql> CREATE DATABASE lite;
Query OK, 1 row affected (0.19 sec)
```

### 使用数据库：

```
mysql> USE lite;
Database changed
```

### 建表：

建表执行：

```
mysql> CREATE TABLE t_user (user_id BIGINT NOT NULL, username VARCHAR(20));
Query OK, 0 rows affected (0.82 sec)
```


### 查看表：

查看数据库中的所有表：

```
mysql> SHOW TABLES;
+----------------+
| Tables_in_lite |
+----------------+
| t_user         |
+----------------+
1 row in set (0.00 sec)
```


查看表的详情：

```
mysql> DESCRIBE t_user;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| user_id  | bigint(20)  | NO   |     | NULL    |       |
| username | varchar(20) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
2 rows in set (0.00 sec)
```

### 插入数据：

```
mysql> INSERT INTO t_user(user_id, username) VALUES(1, '老卫');
Query OK, 1 row affected (0.08 sec)
```