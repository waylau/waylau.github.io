---
layout: post
title: MySQL 9 在 Windows 下安装及使用    
date: 2025-05-29 00:22
author: admin
comments: true
categories: [MySQL]
tags: [MySQL]
---

MySQL 9 带来了全新的体验，比如新增向量数据类型（VECTOR Type），支持存储多维数据（如数组），适用于机器学习、数据科学等对高维数据处理有需求的场景。MySQL 9.0在功能扩展、性能优化与安全性提升方面做出了显著改进，并引入了多项现代化技术以支持复杂应用场景。本文讲解如何在 Windows 下安装 MySQL 9，以及基本的 MySQL 用法。

<!-- more -->


## 下载

下载地址 <https://dev.mysql.com/downloads/mysql/9.html>。

本例为：MySQL Community Server 9.3.0。

## 解压

解压至安装目录，比如 D 盘根目录下。

本例为：`D:\dev\database\mysql-9.3.0-winx64`。

## 创建 my.ini 

my.ini 是 MySQL 安装的配置文件：

```
[mysqld]
# 安装目录
basedir=D:\\dev\database\mysql-9.3.0-winx64
# 数据存放目录
datadir=D:\\data\mysql
```


my.ini放置在 MySQL 安装目录的根目录下。需要注意的是，要先创建`D:\\data\mysql`目录。


## 初始化安装

执行：

```
mysqld --defaults-file=D:\dev\database\mysql-9.3.0-winx64\my.ini --initialize --console
```

控制台输出如下，说明安装成功：

```
D:\dev\database\mysql-9.3.0-winx64\bin>mysqld --defaults-file=D:\dev\database\mysql-9.3.0-winx64\my.ini --initialize --console
2025-05-29T07:42:02.917880Z 0 [System] [MY-015017] [Server] MySQL Server Initialization - start.
2025-05-29T07:42:02.927349Z 0 [System] [MY-013169] [Server] D:\dev\database\mysql-9.3.0-winx64\bin\mysqld.exe (mysqld 9.3.0) initializing of server in progress as process 23776
2025-05-29T07:42:03.006423Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2025-05-29T07:42:03.471840Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2025-05-29T07:42:05.948650Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: ch#Ey0tB%TY2
2025-05-29T07:42:09.190933Z 0 [System] [MY-015018] [Server] MySQL Server Initialization - end.
```


其中，“ch#Ey0tB%TY2”就是 root 用户的初始化密码。稍后可以做更改。

## 启动、关闭 MySQL server

执行mysqld就能启动 MySQL server，或者执行 mysqld --console可以看到完整的启动信息：

```
D:\dev\database\mysql-9.3.0-winx64\bin>mysqld --console
2025-05-29T07:45:26.490001Z 0 [System] [MY-015015] [Server] MySQL Server - start.
2025-05-29T07:45:26.773383Z 0 [System] [MY-010116] [Server] D:\dev\database\mysql-9.3.0-winx64\bin\mysqld.exe (mysqld 9.3.0) starting as process 5472
2025-05-29T07:45:26.803304Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2025-05-29T07:45:27.210571Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2025-05-29T07:45:27.743931Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2025-05-29T07:45:27.744233Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2025-05-29T07:45:27.791234Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060
2025-05-29T07:45:27.791433Z 0 [System] [MY-010931] [Server] D:\dev\database\mysql-9.3.0-winx64\bin\mysqld.exe: ready for connections. Version: '9.3.0'  socket: ''  port: 3306  MySQL Community Server - GPL.
```


关闭，可以执行 `mysqladmin -u root shutdown`。


## 使用 MySQL 客户端

使用 mysql 来登录，账号为 root，密码为“ch#Ey0tB%TY2”：

```
D:\dev\database\mysql-9.3.0-winx64\bin>mysql -u root -p
Enter password: ************
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 9.3.0

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

执行下面的语句来改密码。其中“123456”即为新密码。

```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
Query OK, 0 rows affected (0.13 sec)
```

## MySQL 常用指令

### 显示已有的数据库：

```
mysql> SHOW DATABASES;
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
mysql> CREATE DATABASE test_db;
Query OK, 1 row affected (0.19 sec)
```

### 使用数据库：

```
mysql> USE test_db;
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
+-------------------+
| Tables_in_test_db |
+-------------------+
| t_user            |
+-------------------+
1 row in set (0.157 sec)
```


查看表的详情：

```
mysql> DESCRIBE t_user;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| user_id  | bigint      | NO   |     | NULL    |       |
| username | varchar(20) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
2 rows in set (0.036 sec)
```

### 插入数据：

```
mysql> INSERT INTO t_user(user_id, username) VALUES(1, '老卫');
Query OK, 1 row affected (0.08 sec)
```


### 查询数据：

```
mysql> SELECT * FROM t_user;
+---------+----------+
| user_id | username |
+---------+----------+
|       1 | 老卫     |
+---------+----------+
1 row in set (0.014 sec)
```