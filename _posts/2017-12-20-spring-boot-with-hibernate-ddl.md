---
layout: post
title: Spring Boot 设置 Hibernate DDL
date: 2017-12-20 00:22
author: admin
comments: true
categories: [Spring Boot,Hibernate]
tags: [Spring Boot,Hibernate]
---

在 “[基于Spring Boot 企业级博客系统实战](https://waylau.com/spring-boot-blog-video-release/) ” 课程中， 有学员对课程中所使用的 Hibernate DDL 表示不解决。特作答如下。

<!-- more -->


在课程中，我们经常推荐如下配置：

```
spring.jpa.hibernate.ddl-auto=create-drop
```

使用该配置，Spring Boot 应用在启动过程中，就能根据实体，来自动映射成为数据库的表结构。create-drop 意味着，创建表结构前，会把之前已经存在的表结构先 drop掉。好处在于，每次的表结构都是全新的。

当然，也有学员产生了困扰。好不容易在表结构中创建了一堆的数据，结果重启了应用之后，就都drop掉，不见了。非常着急。

其实，很简单，只需设置 spring.jpa.hibernate.ddl-auto 配置即可。spring.jpa.hibernate.ddl-auto 的配置与 Hibernate 相似，除了 create-drop 外，还有 none, validate, update, create 等几个配置项。每个项含义如下：

* validate 加载 Hibernate 时，验证创建数据库表结构
* create 每次加载 Hibernate ，重新创建数据库表结构，这就是导致数据库表数据丢失的原因。
* create-drop 加载 Hibernate 时创建，退出是删除表结构
* update 加载 Hibernate 自动更新数据库结构

所以，如果你想保留表结构的数据，使用 update 即可。