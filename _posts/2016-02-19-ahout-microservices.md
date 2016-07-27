---
layout: post
title: 简述 Microservices（微服务）
date: 2016-02-19 02:41
author: admin
comments: true
categories: [Microservices]
tags: [Microservices,微服务]
---
自 2014 年始，Microservices（微服务）一词越来越火爆，不谈 Microservices 彷佛就 out 了。那么什么是 Microservices？Microservices 架构与传统的架构有什么区别？何时应该采用 Microservices？如何构建 Microservices？

本文，就针对上述提到的问题，来简单介绍下 Microservices。

<!-- more -->

## 什么是 Microservices

微服务的诞生并非偶然: [领域驱动设计](http://dddcommunity.org/)指导我们如何分析并模型化复杂的业务；[敏捷方法论](http://agilemethodology.org/)帮助我们消除浪费，快速反馈；[持续交付](http://agilemethodology.org/)促使我们构建更快、更可靠、更频繁的软件部署和交付能力；虚拟化和基础设施自动化(Infrastructure As Code)则帮助我们简化环境的创建、安装；[DevOps](http://dev2ops.org/2010/02/what-is-devops/) 文化的流行以及特性团队的出现，使得小团队更加全功能化。这些都是推动微服务诞生的重要因素。

实际上，业界对于微服务本身并没有一个严格的定义。James Lewis 和 Martin Fowler 对 Microservices 架构做了如下定义：

*简言之，Microservices 架构风格就像是把小的服务开发成单一应用的形式， 运行在其自己的进程中，并采用轻量级的机制进行通信（一般是 HTTP 资源 API）。这些服务都是围绕业务能力来构建，通过全自动部署工具来实现独立部署。这些服务，其可以使用不同的编程语言和不同的数据存储技术，并保持最小化集中管理。*
   
Microservices 包含如下特征：

* **组件以服务形式来提供**：正如其名，微服务也是面向服务的。
* **围绕业务功能进行组织**：微服务更倾向于围绕业务功能对服务结构进行划分、拆解。这样的服务，是针对业务领域有着关完整实现的软件，它包含使用接口、持久存储、以及对旬的交互。因此团队应该是跨职能的，包含完整的开发技术：用户体验、数据库、以及项目管理。
* **产品不是项目**：传统的开发模式，是至力于提供一些被认为是完整的软件。一旦开发完成，软件将移交给维护或者实施部门，然后，开发组就可以解散掉了。而微服务要求开发团队对软件产品的整个生命周期负责。这要求开发者每天都关注软件产品的运行情况，并与用户联系的更紧密，同时承担一些售后支持。越小的服务粒度越容易促进用户与服务提供商之前的关系。Amazon 的理念就是“[You build, you run it](http://blog.avisi.nl/2013/09/06/you-build-it-you-run-it/)”，这也正是 DevOps 的文化理念。
* **强化终端及弱化通道**：微服务的应用致力松耦合和高内聚，它们更喜欢简单的REST 风格，而不是复杂的协议（如WS或者BPEL或者集中式框架）。或者采用轻量级消息总线（如 RabbitMQ 或 ZeroMQ 等）来发布消息。
* **分散治理**：这是跟传统的集中式管理很大区别的地方。微服务把整体式框架中的组件，拆分成不同的服务，在构建它们时将会有更多的选择。
* **分散数据管理**：当整体式的应用使用单一逻辑数据库对数据持久化时，企业通常选择在应用的范围内使用一个数据库。微服务让每个服务管理自己的数据库：无论是相同数据库的不同实例，或者是不同的数据库系统。
* **基础设施自动化**：云计算，特别是 AWS 的发展，减少了构建、发布、运维微服务的复杂性。微服务的团队更加依赖于基础设施的自动化，毕竟发布工作相当的无趣。近些年开始火爆的 Docker 也是一个不错的选择（可以参阅《[简述 Docker](http://waylau.com/ahout-docker/)》）。
* **容错性设计**：任务服务都可能因为供应商的不可靠而故障。微服务应为每个应用的服务及数据中心提供日常故障检测和恢复。
* **改进设计**：由于设计会不断更改，微服务所提供的服务应该能够替换或者报废，而不是要长久的发展的。


## MSA vs. SOA

微服务架构（MSA）与 面向服务架构（SOA）相似之处，比如，都是面向服务。通常 SOA 意味着大而全的整体单块架构系统(monolithic)的解决方案。这让设计、开发、测试、发布都增加了难度，其中任何细小的代码变更，都将导致整个系统的需要重新测试，部署。而微服务架构恰恰把所有服务都打散，设置合理的颗粒度，各个服务间保持低耦合，每个服务都在其完整的生命周期中存活，互相之间影响降到最低。

SOA 需要对整个系统进行规范，而 MSA 每个服务都可以有自己的开发语言、开发方式，灵活性大大提高。

## 何时采用 Microservices

对于分布式设计来说，分布式第一定律是“尽量不要使用分布式”。因为系统的分布式一定会带来性能的开销。

微服务使得开发变得更简单，快捷了。以前开发人员耗费时间来搭建环境、熟悉代码结构，在微服务的世界里会简单许多。但是，微服务带来了一系列的非功能性需求，比如说事务、服务治理（注册，发现，负载，路由，认证授权，隔离）、监控（日志，性能监控，告警，调用链路）、部署、测试等。微服务依赖于“基础设施自动化”。

微服务不是“银弹”，何时采用微服务还需考虑企业自身的需求。

## 如何构建 Microservices

真是一个大话题，本文不会详细涉及。笔者在《[REST 实战](https://github.com/waylau/rest-in-action/)》的 “使用 Java SE 部署环境”一章节中，写一个结合 Jetty 、Tomcat、Jersey 等技术，实现了 REST 风格 API 的 Microservices 入门例子。

如果对 Microservices 抱有兴趣，可以参阅市面上的书籍：

* 《[Building Microservices](http://book.douban.com/subject/25881698/)》（Sam Newman）
* 《[Microservices: Patterns and Applications](http://book.douban.com/subject/26385465/)》（Lucas Krause ）
* 《[微服务架构与实践](http://book.douban.com/subject/26693152/)》（王磊）

## 参考

* <http://www.infoq.com/cn/articles/enterprise-core-systems-transformation-practice/>
* <http://martinfowler.com/articles/microservices.html>
* <http://waylau.com/ahout-docker/>
* 《[REST 实战](https://github.com/waylau/rest-in-action/)》