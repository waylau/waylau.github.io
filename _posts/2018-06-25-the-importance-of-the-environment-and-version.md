---
layout: post
title: 论代码所需要的环境、版本的重要性
date: 2018-06-25 00:22
author: admin
comments: true
categories: [Spring Cloud,Spring Boot]
tags: [Spring Cloud,Spring Boot]
---

学员们在参与“[基于Spring Boot的博客系统实战](http://coding.imooc.com/class/125.html)”课程的时候，可能没有太注意版本的问题。其实，版本是一个非常重要也是一个非常容易忽略的问题。

<!-- more -->

版本不一致会导致各种奇怪的问题，比如：

 * 应用启动不了
 * 启动过程中报错
 * 提示找不到 class
 * 功能不正常
 * 等等

初级学员往往不重视软件版本，存在随意更改版本的现象，从而导致上述问题。同时，学员又缺乏调试程序的能力，一旦出错，将不知所措。认为代码都是跟老师的一样的啊，但是怎么就运行不成功呢？

所以，环境、版本需要纳入和代码同等重要的位置。从某种意义上来讲，环境配置、版本配置都是源码，都要纳入源码管理系统之中。

以下，是课程中学员经常犯的常见的由于环境、版本不一致所导致的问题：

 * [Spring RestTemplate 调用天气预报接口乱码的解决](https://www.imooc.com/article/25569)
 * [Spring Data Elasticsearch与ES的关系](https://www.imooc.com/article/23405)
 * [使用 Bootstrap 4 正式版重新定义网站的新Style！](https://www.imooc.com/article/22881)
 * [Elasticsearch 使用中文分词](https://www.imooc.com/article/20637)
 * [Spring Data Elasticsearch与Elasticsearch的版本关系](https://www.imooc.com/article/19873)
 * [Spring Boot自定义版本](https://www.imooc.com/article/19822)

学员们可以对照检查。

## 如何避免此类错误

简单一句话“莫装逼”。如果能力还不够的话，建议严格按照课程的所采用的版本来。因为课程中所有的软件版本、环境都是经过老师严格测试，并确保可用的。避免此类错误，有效节省学员试错的时间。时间就是money哦~

以下是《基于Spring Boot的博客系统实战》课程所使用的版本：


```
* JDK 8
* Gradle 3.5
* Eclipse Neon.2 Release (4.6.2)：本书示例采用Eclipse编写，但示例源码与具体的IDE无关，读者朋友可以自行选择适合自己的IDE，比如IntelliJ IDEA、NetBeans等。
* Spring Boot 1.5.2.RELEASE
* Thymeleaf 3.0.3.RELEASE
* Thymeleaf Layout Dialec 2.2.0
* MySQL Community Server 5.7.17
* MySQL Workbench 6.3.9
* Spring Data JPA 1.11.1.RELEASE
* Hibernate 5.2.8.Final
* MySQL Connector/J 6.0.5
* H2 Database 1.4.193
* Elasticsearch 2.4.4
* Spring Data Elasticsearch 2.1.3.RELEASE
* JNA 4.3.0
* Tether 1.4.0 :<http://tether.io/>
* Bootstrap v4.0.0-alpha.6 : <https://v4-alpha.getbootstrap.com/>
* jQuery 3.1.1 : <http://jquery.com/download/>
* Font Awesome 4.7.0 :<http://fontawesome.io>
* NProgress 0.2.0 :<http://ricostacruz.com/nprogress/>
* Thinker-md :<http://git.oschina.net/benhail/thinker-md>
* jQuery Tags Input 1.3.6 : <http://xoxco.com/projects/code/tagsinput/>
* Bootstrap Chosen 1.0.3 :<https://github.com/haubek/bootstrap4c-chosen>
* toastr 2.1.1 :<http://www.toastrjs.com/> 
* Spring Security 4.2.2.RELEASE
* Thymeleaf Spring Security 3.0.2.RELEASE
* Apache Commons Lang 3.5
* Markdown parser for the JVM 0.16  
* MongoDB 3.4.4
* Embedded MongoDB 2.0.0
```

当对讲师的代码熟悉了之后，同时，掌握了调错的能力之后，学员就可以根据自己的情况来调整版本了。 比如，将 Spring Boot 升级到 Spring Boot 2 、使用最新的 ES 版本等。

## 想学 Spring Boot 2 

目前，上述课程是基于 Spring Boot 1.5.2 来讲解的，新版本的 Spring Boot 大致上也是差不多的。学员可以举一反三。

如果想直接学 Spring Boot 2 ，可以关注老师出版的另外一门书《Spring Boot 企业级应用开发实战》（<https://book.douban.com/subject/30192752/>），基于最新的 Spring Boot 2 来展开。可以理解为是上述课程的升级。对课程的版本进行了升级，同时补充了课程中无法展开的知识点的梳理。

该书所使用的版本如下：

```
* JDK 8
* Gradle 4.0
* Eclipse Oxygen Release (4.7.0)：本书示例采用 Eclipse 编写，但示例源码与具体的 IDE 无关，读者朋友可以自行选择适合自己的 IDE，比如 IntelliJ IDEA、NetBeans 等。
* Spring Boot 2.0.0.M2
* Spring 5.0.0.RC2
* Thymeleaf 3.0.6.RELEASE
* Thymeleaf Layout Dialect 2.2.2
* MySQL Community Server 5.7.17
* MySQL Workbench 6.3.9
* Spring Data JPA 2.0.0.M4
* Hibernate 5.2.10.Final
* MySQL Connector/J 6.0.5
* H2 Database 1.4.196
* Elasticsearch 5.5.0
* Spring Data Elasticsearch 3.0.0.M4
* Tether 1.4.0 :<http://tether.io/>
* Bootstrap v4.0.0-alpha.6 : <https://v4-alpha.getbootstrap.com/>
* jQuery 3.1.1 : <http://jquery.com/download/>
* Font Awesome 4.7.0 :<http://fontawesome.io>
* NProgress 0.2.0 :<http://ricostacruz.com/nprogress/>
* Thinker-md :<http://git.oschina.net/benhail/thinker-md>
* jQuery Tags Input 1.3.6 : <http://xoxco.com/projects/code/tagsinput/>
* Bootstrap Chosen 1.0.3 :<https://github.com/haubek/bootstrap4c-chosen>
* toastr 2.1.1 :<http://www.toastrjs.com/> 
* Spring Security 5.0.0.M2
* Thymeleaf Spring Security 3.0.2.RELEASE
* Apache Commons Lang 3.6
* Markdown parser for the JVM 0.16  
* MongoDB 3.4.6
* Spring Data Mongodb 2.0.0.M4
* Embedded MongoDB 2.0.0
* IK Analysis for Elasticsearch 5.5.0
```

## 看视频还是看书

从我个人来看，看视频、看书是两种非常不同的学习方式。视频更加生动且易于理解。而书则更加全面且方便回顾。

如何选择看视频还是看书来学习，取决于个人学习习惯。当然，学习是不嫌多的，如果有充足的时间不妨书和视频一起结合起来看。

老卫课程的优点就是，所有的视频课程，都是会有相关配套的书籍提供的，以利于学员知识点的回顾。视频、看书两不误，双管齐下，老卫更懂你。

以下是老卫的课程，以及对应的书籍：

视频课程 | 对应书籍
--- | ---
[基于Spring Boot的博客系统实战](http://coding.imooc.com/class/125.html) | [Spring Boot 企业级应用开发实战](https://book.douban.com/subject/30192752)
[基于Spring Cloud的微服务实战](https://coding.imooc.com/class/177.html) | [Spring Cloud 微服务架构开发实战](https://book.douban.com/subject/30245248/)