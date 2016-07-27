---
layout: post
title: Spring 4.x 与可选的第三方库的关系
date: 2014-07-26 03:24
author: admin
comments: true
categories: [Java,Spring]
tags: [Java,Spring]
---
1.`Spring 4`唯一的外部强制性的依库是 jakarta `commons-logging`

2.`Spring 4`与可选的第三方库一般只支持在2010年底或后来发布的版本：特别是，`Hibernate 3.6+`, `EhCache 2.1+`, `Quartz 1.8+`, `Groovy 1.8+`, and `Joda-Time 2.0+`。作为另外，`Spring 4` 要求最近的`Hibernate Validator 4.3 +`，并支持`Jackson 2.0+` （`Jackson 1.8/1.9`只保留对 `Spring 3.2` 支持；现在是处在废弃的状态）。

3.`Spring 4` 对低支持到 `JDK 6 update 18`，在新项目中，最好用`Java 7/8`.

4.`Java EE 6/7`现在被认为是`Spring 4`的最低标准。建议使用`JPA 2`
和`Servlet 3`规范。为了兼容`Google
App Engine`和老旧的应用，它可以部署 `Spring4`应用到`servlet
2.5`环境。然而，强烈建议用 `Servlet 3+`作为测试环境

5.`Gradle`作为项目构建工具

参考：spring-framework-reference ([v4.0.6Release](http://docs.spring.io/spring/docs/4.0.6.RELEASE/spring-framework-reference/htmlsingle/))