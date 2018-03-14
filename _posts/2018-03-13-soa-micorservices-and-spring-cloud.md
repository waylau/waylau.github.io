---
layout: post
title: 服务化、微服务及Spring Cloud
date: 2018-03-13 00:22
author: admin
comments: true
categories: [Spring Cloud，Microservices]
tags: [Spring Cloud，Microservices]
---

作为职场老手的你，也许对服务化（SOA）并不陌生。当今 Web 2.0 架构如火如荼，越来越多的企业正在进行着服务化的改造，最为著名莫过于 Amazon 。

<!-- more -->
 

## 服务化

早在2002年，Amazon 就已经开始进行服务化的改造了。Amazon 的 CEO杰夫·贝索斯对市场有着超乎常人的理解和预见：


* 从今天起，所有的团队都要以服务接口的方式，提供数据和各种功能。
* 团队之间必须通过接口来通信。
* 不允许任何其他形式的互操作：不允许直接链接，不允许直接读其他团队的数据，不允许共享内存，不允许任何形式的后门。唯一许可的通信方式，就是通过网络调用服务。
* 具体的实现技术不做规定，HTTP、Corba、PubSub、自定义协议皆可。
* 所有的服务接口，必须从一开始就以可以公开作为设计导向，没有例外。这就是说，在设计接口的时候，就默认这个接口可以对外部人员开放，没有讨价还价的余地。
* 不遵守上面规定，就开除

Amazon  用自己的行动向世人证明了服务化的是正确的。Amazon  将现有的卖书送书的基础设施，转变成了一个非常出色、可定制的计算平台，让用户付费使用。而这一切都归功于整个基础设施已经改造成了面向服务的架构。Amazon  从一个网上书城华丽转身为全球最大的云服务供应商。

## 微服务

感谢 James Lewis 和 Martin Fowler 对 微服务（Microservices）架构所做的定义：

***简言之，Microservices 架构风格就像是把小的服务开发成单一应用的形式， 运行在其自己的进程中，并采用轻量级的机制进行通信（一般是 HTTP 资源 API）。这些服务都是围绕业务能力来构建，通过全自动部署工具来实现独立部署。这些服务，其可以使用不同的编程语言和不同的数据存储技术，并保持最小化集中管理。***

Microservices 包含如下特征：

* 组件以服务形式来提供：正如其名，微服务也是面向服务的。
* 围绕业务功能进行组织：微服务更倾向于围绕业务功能对服务结构进行划分、拆解。这样的服务，是针对业务领域有着关完整实现的软件，它包含使用接口、持久存储、以及对旬的交互。因此团队应该是跨职能的，包含完整的开发技术：用户体验、数据库、以及项目管理。
* 产品不是项目：传统的开发模式，是至力于提供一些被认为是完整的软件。一旦开发完成，软件将移交给维护或者实施部门，然后，开发组就可以解散掉了。而微服务要求开发团队对软件产品的整个生命周期负责。这要求开发者每天都关注软件产品的运行情况，并与用户联系的更紧密，同时承担一些售后支持。越小的服务粒度越容易促进用户与服务提供商之前的关系。Amazon 的理念就是“You build, you run it”，这也正是 DevOps 的文化理念。
* 强化终端及弱化通道：微服务的应用致力松耦合和高内聚，它们更喜欢简单的REST 风格，而不是复杂的协议（如WS或者BPEL或者集中式框架）。或者采用轻量级消息总线（如 RabbitMQ 或 ZeroMQ 等）来发布消息。
* 分散治理：这是跟传统的集中式管理很大区别的地方。微服务把整体式框架中的组件，拆分成不同的服务，在构建它们时将会有更多的选择。
* 分散数据管理：当整体式的应用使用单一逻辑数据库对数据持久化时，企业通常选择在应用的范围内使用一个数据库。微服务让每个服务管理自己的数据库：无论是相同数据库的不同实例，或者是不同的数据库系统。
* 基础设施自动化：云计算，特别是 AWS 的发展，减少了构建、发布、运维微服务的复杂性。微服务的团队更加依赖于基础设施的自动化，毕竟发布工作相当的无趣。近些年开始火爆的 Docker 也是一个不错的选择（可以参阅《简述 Docker》<https://waylau.com/ahout-docker/>）。
* 容错性设计：任务服务都可能因为供应商的不可靠而故障。微服务应为每个应用的服务及数据中心提供日常故障检测和恢复。
* 改进设计：由于设计会不断更改，微服务所提供的服务应该能够替换或者报废，而不是要长久的发展的。



那么 SOA 与 微服务到底有哪些差异呢？

### MSA vs. SOA

微服务架构（MSA）与 面向服务架构（SOA）相似之处，比如，都是面向服务。通常 SOA 意味着大而全的整体单块架构系统(monolithic)的解决方案。这让设计、开发、测试、发布都增加了难度，其中任何细小的代码变更，都将导致整个系统的需要重新测试，部署。而微服务架构恰恰把所有服务都打散，设置合理的颗粒度，各个服务间保持低耦合，每个服务都在其完整的生命周期中存活，互相之间影响降到最低。

SOA 需要对整个系统进行规范，而 MSA 每个服务都可以有自己的开发语言、开发方式，灵活性大大提高。

## Spring Cloud 

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

有关 Spring Boot 内容，可见笔者的视频课程《基于Spring Boot的博客系统实战》（<http://coding.imooc.com/class/125.html>）。

### Spring Cloud 与微服务

Spring Cloud 是构建分布式系统的利器，而微服务是当下最火热的分布式系统的类型之一，所以，Spring Cloud 天然是支持微服务的构建的。

在早些年，国内互联网公司盛行采用 Dubbo 来架构微服务。如今，有了更好的选择，那就是 Spring Cloud。有数据显示，Spring Cloud不管是在国内，还是国外，用户数都呈现出爆发式增长。而且，Dubbo 主要只是为了解决服务通信、服务注册等问题，而 Spring Cloud 却是提供微服务架构的完整的解决方案。


### Spring Cloud 如何实现微服务

说了那么多理论，那么微服务架构如何真实的落地呢？课程
《基于Spring Cloud的微服务实战》（<https://coding.imooc.com/class/177.htmll>）给出了真实的答案。

在《基于Spring Cloud的微服务实战》课程中，作者基于Spring Boot + Spring Cloud 技术栈来实现了一个完整的天气预报系统。在课程中，先从 Spring Boot 入手，从0到1 快速搭建了具备高并发能力、界面友好的天气预报系统。而后剖析单块架构的利弊，从而引入微服务架构的概念，并实从1到0实现微服务的拆分。最后引入Spring Cloud 技术来实现对这些微服务的治理，重点讲解了服务注册与发现、服务交互、服务消费、负载均衡、API网关、配置中心、服务熔断、自动扩展等方面的话题。

![](https://waylau.com/images/post/20180122-spring-cloud-001.png)

通过学习该课程，学员不但可以学会 Spring Boot 及 Spring Cloud 最新的周边技术栈（本课程基于最新的 Spring Boot 2.0.0.M4 以及 Spring Cloud Finchley.M2），掌握如何运用上述技术进行整合，搭建框架的能力，熟悉单体架构及微服务架构的特点，并最终实现掌握构建微服务架构的实战能力。

### 搭建 Spring Cloud 微服务系统需要哪些技术

《基于Spring Cloud的微服务实战》（<https://coding.imooc.com/class/177.htmll>）课程所涉及的相关的技术有 ：

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

要学习 Spring Cloud 微服务，除了上面的课程之前，老卫还撰写了多门微服务相关的开源书籍（<https://waylau.com/books/>），注意，都是免费的书籍哦！！！

* 简述 Microservices（微服务）：<https://waylau.com/ahout-microservices/>
* Spring Boot 教程：<https://github.com/waylau/spring-boot-tutorial>
* Spring Cloud 教程：<https://github.com/waylau/spring-cloud-tutorial>
* Gradle 3 用户指南：<https://github.com/waylau/gradle-3-user-guide>
* Spring Security 教程：<https://github.com/waylau/spring-security-tutorial>
* Thymeleaf 教程：<https://github.com/waylau/thymeleaf-tutorial>