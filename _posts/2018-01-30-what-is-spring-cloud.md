---
layout: post
title: 简述 Spring Cloud 是什么
date: 2018-01-30 00:22
author: admin
comments: true
categories: [Spring Cloud,Spring Boot,Microservices]
tags: [Spring Cloud,Spring Boot,Microservices,微服务]
---

很多同学都了解了Spring ，了解了 Spring Boot, 但对于 Spring Cloud 是什么还是比较懵逼的。

本文带你简单的了解下，什么是Spring Cloud。


<!-- more -->


## Spring Cloud 是什么

从字面理解，Spring Cloud 就是致力于分布式系统、云服务的框架。

Spring Cloud 是整个 Spring 家族中新的成员，是最近云服务火爆的必然产物。

Spring Cloud 为开发人员提供了快速构建分布式系统中一些常见模式的工具，例如：

* 配置管理
* 服务注册与发现
* 断路器
* 智能路由
* 服务间调用
* 负载均衡
* 微代理
* 控制总线
* 一次性令牌
* 全局锁
* 领导选举
* 分布式会话
* 集群状态
* 分布式消息
* ......

使用 Spring Cloud 开发人员可以开箱即用的实现这些模式的服务和应用程序。这些服务可以任何环境下运行，包括分布式环境，也包括开发人员自己的笔记本电脑以及各种托管平台。

## Spring Cloud 与 Spring Boot

Spring Cloud 基于 Spring Boot 来进行构建服务。这样，开发Spring Cloud 组件时，就能依托 Spring Boot 来实现快速开发。

有关 Spring Boot 内容，可见笔者的视频课程《基于Spring Boot的博客系统实战》（<https://waylau.com/spring-boot-blog-video-release/>）。

## Spring Cloud 与微服务

Spring Cloud 是构建分布式系统的利器，而微服务是当下最火热的分布式系统的类型之一，所以，Spring Cloud 天然是支持微服务的构建的。

在早些年，国内互联网公司盛行采用 Dubbo 来架构微服务。如今，有了更好的选择，那就是 Spring Cloud。有数据显示，Spring Cloud不管是在国内，还是国外，用户数都呈现出爆发式增长。而且，Dubbo 主要只是为了解决服务通信、服务注册等问题，而 Spring Cloud 却是提供微服务架构的完整的解决方案。

那么什么是微服务？

所谓微服务，就是：

*微服务架构风格就像是把小的服务开发成单一应用的形式， 运行在其自己的进程中，并采用轻量级的机制进行通信（一般是 HTTP 资源 API）。这些服务都是围绕业务能力来构建，通过全自动部署工具来实现独立部署。这些服务，其可以使用不同的编程语言和不同的数据存储技术，并保持最小化集中管理。*

更多有关微服务的理论，可见笔者的博客：<https://waylau.com/ahout-microservices/> 。

## Spring Cloud 如何实现微服务

说了那么多理论，那么微服务架构如何真实的落地呢？课程
《基于Spring Cloud的微服务实战》（<https://waylau.com/spring-cloud-video-release/>）给出了真实的答案。

在《基于Spring Cloud的微服务实战》课程中，作者基于Spring Boot + Spring Cloud 技术栈来实现了一个完整的天气预报系统。在课程中，先从 Spring Boot 入手，从0到1 快速搭建了具备高并发能力、界面友好的天气预报系统。而后剖析单块架构的利弊，从而引入微服务架构的概念，并实从1到0实现微服务的拆分。最后引入Spring Cloud 技术来实现对这些微服务的治理，重点讲解了服务注册与发现、服务交互、服务消费、负载均衡、API网关、配置中心、服务熔断、自动扩展等方面的话题。

![](https://waylau.com/images/post/20180122-spring-cloud-001.png)

通过学习该课程，学员不但可以学会 Spring Boot 及 Spring Cloud 最新的周边技术栈（本课程基于最新的 Spring Boot 2.0.0.M4 以及 Spring Cloud Finchley.M2），掌握如何运用上述技术进行整合，搭建框架的能力，熟悉单体架构及微服务架构的特点，并最终实现掌握构建微服务架构的实战能力。

## 搭建 Spring Cloud 微服务系统需要哪些技术

本课程所涉及的相关的技术有 ：

* XML解析：JABX
* JSON序列化:Jackson
* 缓存：Redis
* 定时器：Quartz Scheduler 
* Java模版技术Thymeleaf 
* 前端样式：Bootstrap
* API网关：Zuul
* 服务注册与发现：Eureka Server、Eureka Client
* 服务交互：RestTemplate、Apache HttpClient
* 服务消费：Ribbon、OpenFeign
* 负载均衡：Ribbon
* 配置中心：Config Server、Config Client
* 服务熔断：Hystrix
* 项目构建：Gradle

通过本课程的学习，能够掌握架构微服务系统的能力！

## 参考资料

要学习 Spring Cloud 微服务，除了上面的课程之前，老卫还撰写了多门微服务相关的开源书籍，注意，都是免费的书籍哦！！！

* 简述 Microservices（微服务）：<https://waylau.com/ahout-microservices/>
* Spring Boot 教程：<https://github.com/waylau/spring-boot-tutorial>
* Spring Cloud 教程：<https://github.com/waylau/spring-cloud-tutorial>
* Gradle 3 用户指南：<https://github.com/waylau/gradle-3-user-guide>
* Spring Security 教程：<https://github.com/waylau/spring-security-tutorial>
* Thymeleaf 教程：<https://github.com/waylau/thymeleaf-tutorial>

小伙伴们，快点学习起来！！！


