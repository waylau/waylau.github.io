---
layout: post
title: JDBC 事务隔离级别
date: 2018-02-10 00:22
author: admin
comments: true
categories: [JDBC]
tags: [JDBC]
---

本文总结了 JDBC 事务隔离级别。 

<!-- more -->
 
事务隔离级别定义了在一个事务中，哪些数据是对当前执行的语句“可见”的。在并发访问数据库时，事务隔离级别定义了多个事务之间对于同个目标数据源访问时的可交叉程度。

可交叉程度可分为以下几类。

## 可交叉程度

### dirty reads(脏读)

当一个事务能看见另外一个事务未提交的数据时，就称为脏读，换言之，一个事务修改数据后再未提交之前，就能被其它事务看见。如果这个事务被回滚了而不是提交了，那么其它事务看到的数据则是不正确的，是“脏”的。

### nonrepeatable reads(不可重复读)

假设事务 A 读取了一行数据，接下来事务 B 改变了这行数据，之后事务 A 又再一次读取这行数据，这时候事务 A 就取到了两个不同的结果。

### phantom reads(幻读)

假设事务 A 通过一个 where 条件读取到了一个结果集，事务 B 这时插入了一条符合事务 A 的 where 条件的数据，之后事务 A 通过同样的 where 条件再次进行查询时，发现了多出来一条数据。


## 事务隔离级别

JDBC 规范增加了 TRANSACTION_NONE 隔离级别，来满足了 SQL:2003 定义的 4 种事务隔离级别。隔离级别从最宽松到最严格，排序如下所示：

### TRANSACTION_NONE

这意味着当前的 JDBC 驱动不支持事务，也意味着这个驱动不符合 JDBC 规范。

### TRANSACTION_READ_UNCOMMITTED

允许事务看到其它事务修改了但未提交的数据，这意味着有可能是脏读、不可重复读或者幻读。

### TRANSACTION_READ_COMMITTED

一个事务在未提交之前，所做的修改不会被其它事务所看见。这能避免脏读，但避免不了不可重复读和幻读。

### TRANSACTION_REPEATABLE_READ

避免了脏读和不可重复读，但幻读依然是有可能发生的。

### TRANSACTION_SERIALIZABLE

避免了脏读、不可重复读以及幻读。


## 参考资料

* [JDBC 4.2 Specification（JSR 221）JDBC 4.2 规范](https://github.com/waylau/jdbc-specification)


