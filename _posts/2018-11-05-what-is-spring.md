---
layout: post
title: Spring 的狭义与广义
date: 2018-11-05 00:22
author: admin
comments: true
categories: [Spring]
tags: [Spring]
---

Java 开发者对于 Spring 应该不会陌生。Spring 可以说是 Java EE 开发事实上的标准。无论是 Web 开发，还是分布式应用，Spring 都致力于简化开发者创建应用的复杂性。本文讨论 Spring 在狭义上以及广义上，所承载的不同的概念。

<!-- more -->

Spring 有广义与狭义之说。

## 狭义上的 Spring——Spring Framework

狭义上的 Spring, 是特指 Spring 框架（Spring Framework）。Spring 框架是为了解决企业应用开发的复杂性而创建的。Spring 框架的主要优势之一就是其分层架构。分层架构允许使用者选择使用哪一个组件，同时为 Java EE 应用程序开发提供集成的框架。Spring 框架使用基本的 POJO 来完成以前只可能由 EJB 完成的事情。然而，Spring 框架的用途不仅限于服务器端的开发。从简单性、可测试性和松耦合的角度而言，任何 Java 应用都可以从 Spring 框架中受益。Spring 框架的核心是控制反转（IoC）和面向切面（AOP）。简单来说，Spring 框架是一个分层的面向与 Java 应用的一站式轻量级开源框架。

Spring 框架前身，是 Rod Johnson 发表在 *Expert One-on-One J2EE Design and Development* 一书中所包含的3万行代码的附件。在书中，他展示了如何在不使用 EJB 的情况下构建高质量、可扩展的在线座位预留应用程序。为了构建该应用程序，他写了超过万行的基础结构代码。这些代码包含了许多可重用的 Java 接口和类，如 ApplicationContext 和 BeanFactory 等。由于 Java 接口是依赖注入的基本构件，因此他将类的根包命名为`com.interface21`，意思是这是一个提供给21世纪的参考！根据书中描述，这些代码已经在一些真实的金融系统中使用。

由于该书影响甚广，当时有几个开发人员 Juergen Hoeller 以及 Yann Caroff 联系上了 Rod Johnson，希望将`com.interface21`代码开源。Yann Caroff 将这个新框架并命名为了“Spring”，意思是就像一缕春风扫平传统 J2EE 的恶冬。所以说，Rod Johnson、Juergen Hoeller 以及 Yann Caroff 是 Spring 框架的共同创立者。

2003年2月，Spring 0.9 发布，采用了 Apache 2.0 开源协议。2004年4月，Spring 1.0 发布。到如今，Spring 框架已经是第5个主要版本了。

## 广义上的 Spring——Spring 技术栈

广义上的 Spring 是指以 Spring 框架为核心的 Spring 技术栈。这些技术栈涵盖了从企业级应用到云计算等各个方面的内容。包括：

* Spring Data：Spring 框架中的数据访问模块对 JDBC 及 ORM 提供了很好的支持。随着 NoSQL 和大数据的兴起，出现了越来越多的新技术，比如非关系型数据库、MapReduce 框架。Spring Data 正是为了让 Spring 开发者能更方便地使用这些新技术而诞生的“大”项目——它由一系列小的项目组成——分别为不同的技术提供支持，例如 Spring Data JPA、Sprng Data Hadoop、Spring Data MongoDB、Spring Data Redis 等等。通过 Spring Data，开发者可以用 Spring 提供的相对一致的方式来访问位于不同类型的数据存储中的数据。
* Spring Batch：一款专门针对企业级系统中的日常批处理任务的轻量级框架，能够帮助开发者方便地开发出强壮、高效的批处理应用程序。通过 Spring Batch 可以轻松构建出轻量级的、健壮的并⾏处理应用，并支持事务、并发、流程、监控、纵向和横向扩展，提供统⼀的接口管理和任务管理。Spring Batch 对批处理任务进行了一定的抽象，它的架构可以大致分为三层，自上而下分别是业务逻辑层、批处理执行环境层和基础设施层。Spring Batch 可以很好地利用 Spring 框架所带来的各种便利，同时也为开发者提供了相对熟悉的开发体验。
* Spring Integration：在企业软件开发过程中，经常会遇到需要与外部系统集成的情况，这时可能会使用 EJB、RMI、JMS 等各种技术，也许你会引入ESB。如果你在开发时用了 Spring 框架，那么不妨考虑下 Spring Integration——它为 Spring 编程模型提供了一个支持企业集成模式的扩展，在应用程序中提供轻量级的消息机制，可以通过声明式的适配器与外部系统进行集成。Spring Integraton 中有几个基本的概念：Message（带有元数据的Java对象）、Channel（传递消息的管道）和Message Endpoint（消息的处理端）。在处理端可以对消息进行转换、路由、过滤、拆分、聚合等操作；更重要的是可以使用 Channel Adapter，这是应用程序与外界交互的地方，输入是 Inbound、输出则是 Outbound，可选的连接类型有很多，比如 AMQP、JDBC、Web Services、FTP、JMS、XMPP、多种 NoSQL 数据库等。只需通过简单的配置文件就能将所有这些东西串联在一起，实现复杂的集成工作。
* Spring Security：前身是 Acegi，是较为成熟的子项目之一，是一款可定制化的身份验证和访问控制框架。读者朋友如果对该技术有兴趣，可以参阅笔者所著的开源书《Spring Security 教程》（<https://github.com/waylau/spring-security-tutorial>）以了解更多 Spring Security 方面的内容。
* Spring Mobile：对 Spring MVC 的扩展，旨在简化移动 Web 应用的开发。
* Spring for Android：用于简化 Android 原生应用程序开发的 Spring 扩展。
* Spring Boot：是 Spring 团队提供的全新框架，其设计目的是用来简化新 Spring 应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。Spring Boot 为 Spring 平台及第三方库提供开箱即用的设置，这样你就可以有条不紊地来进行应用的开发。多数 Spring Boot 应用只需要很少的 Spring 配置。通过这种方式，Spring Boot 致力于在蓬勃发展的快速应用开发领域成为领导者。读者朋友如果对该技术有兴趣，可以参阅笔者所著的开源书《Spring Boot 教程》（<https://github.com/waylau/spring-boot-tutorial>）是了解更多 Spring Boot 方面的内容。本书的“第25章 Spring Boot”也会对 Spring Boot 做深入的探讨。
* Spring Cloud：使用 Spring Cloud，开发人员可以开箱即用的实现分布式系统中常用的服务。这些服务可以任何环境下运行，包括分布式环境，也包括开发人员自己的笔记本电脑、裸机数据中心，以及 Cloud Foundry 等托管平台。Spring Cloud 基于 Spring Boot 来进行构建服务，并可以轻松地集成第三方类库，来增强应用程序的行为。读者如果对该技术有兴趣，可以参阅笔者所著的开源书《Spring Cloud 教程》（<https://github.com/waylau/spring-cloud-tutorial>）是了解更多 Spring Cloud 方面的内容。本书的“第26章 Spring Cloud”也会对 Spring Cloud 做深入的探讨。
* ......


Spring 的技术栈还有很多，读者朋友有兴趣的话，可以访问笔者所著的“Spring 5案例大全”项目（<https://github.com/waylau/spring-5-book）了解更多信息。

## 约定

由于 Spring 是早期 Spring 框架的总称，所以，有时候这个“Spring”这个命名会给读者产生困扰。一般地，我们约定“Spring 框架”特指是狭义上的 Spring，即 Spring Framework；而“Spring”特指是广义上的 Spring，泛指 Spring 技术栈。

## 参考引用

* 《Spring 5 开发大全》：<https://book.douban.com/subject/30370024/>
