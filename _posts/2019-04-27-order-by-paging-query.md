---
layout: post
title: 分页查询最好加排序（order by）
date: 2019-04-27 00:22
author: admin
comments: true
categories: [SQL,Oracle]
tags: [SQL,Oracle]
---

昨天，与外部化系统对接时，发现有一个数据一直咩有集成到，双方各自排查了自己系统的代码，都觉得逻辑非常简单，无法就是一个分页查询而已。

问题就出在这个分页查询上。

<!-- more -->

为了说明当时问题发生的情景，我模拟了一个SQL查询：


```sql
SELECT ID, OBJECT_NAME, OWNER
FROM 
 (
 SELECT ROWNUM RN, ID, OBJECT_NAME, OWNER
 FROM 
 (
 SELECT OWNER, OBJECT_NAME, ID
 FROM MY_TABLE
 )
 WHERE ROWNUM <= 800
 )
WHERE RN >= 1;
```

以上是一段典型的Oracle数据库的分页查询，又以查询结果集的第1条至第800条数据。

如果想查询第二页的数据，只需要改到RN和ROWNUM即可。以下是查第二页的数据的SQL：

```sql
SELECT ID, OBJECT_NAME, OWNER
FROM 
 (
 SELECT ROWNUM RN, ID, OBJECT_NAME, OWNER
 FROM 
 (
 SELECT OWNER, OBJECT_NAME, ID
 FROM MY_TABLE
 )
 WHERE ROWNUM <= 1600
 )
WHERE RN >= 801;
```

## 问题背景

上述SQL中从MY_TABLE查询结果集本身是没有问题的，因为符合条件的数据总数是不会变的。但是有一个问题，从MY_TABLE查询的结果集是没有做排序的，意味着每次查询的数据的位置可能发生变化。由于分页查询，其实是从MY_TABLE查询查询了多次，每次数据的位置不确定，导致查询出来的结果，有可能查得出来，有可能查询重复。这就是问题的根源。



## 解决方案

知道问题所在解决起来就简单了，就是加排序。让每次查询出来的数据按到固定位置查出来就没有了。以下是示例：

```sql
SELECT ID, OBJECT_NAME, OWNER
FROM 
 (
 SELECT ROWNUM RN, ID, OBJECT_NAME, OWNER
 FROM 
 (
 SELECT OWNER, OBJECT_NAME, ID
 FROM MY_TABLE
 ORDER BY ID
 )
 WHERE ROWNUM <= 1600
 )
WHERE RN >= 801;
```

一般来说，排序的字段最好是唯一值。如果是非唯一的字段，那么最好用多个能确定唯一性的字段来实现排序也是可以的。否则无法启动排序的效果，问题还是会出现。
