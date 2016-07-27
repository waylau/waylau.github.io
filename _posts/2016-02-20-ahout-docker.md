---
layout: post
title: 简述 Docker
date: 2016-02-20 02:41
author: admin
comments: true
categories: [Docker]
tags: [Docker,Golang,Microservices,微服务,容器]
---
Docker 是 Golang 编写的， 自 2013 年推出以来，受到越来越多的开发者的关注。如果你关注最新的技术发展，那么你一定听说过 Docker。不管是云服务还是微服务（Microservices），越来越多的厂商都开始基于 Docker 作为基础设施自动化的工具。那么什么是 Docker？Docker与传统的虚拟机有什么区别？为何要采用 Docker？如何使用 Docker？

本文，就针对上述提到的问题，来简单介绍下 Docker。

<!-- more -->

## 什么是 Docker

[Docker](http://www.docker.com/) 是[开源](https://github.com/docker/docker)的应用容器引擎。

Docker 可以让你将所有应用软件以及它的以来打包成软件开发的标准化单元。

Docker 容器将软件以及它运行安装所需的一切文件（代码、运行时、系统工具、系统库）打包到一起，这就保证了不管是在什么样的运行环境，总是能以相同的方式运行。就好像 Java 虚拟机一样，“[一次编写，到处运行（Write once, run anywhere）](https://en.wikipedia.org/wiki/Write_once,_run_anywhere)”，而 Docker 是“一次构建，到处运行（Build once，run anywhere）”。

![](http://www.docker.com/sites/default/files/products/what_is_layered_filesystems_sm.png)


Docker 是一种“容器即服务”（Docker Containers as a Service ，简称 CaaS），使得开发和IT运营团队可以对于应用的构建、发布、运行更加敏捷和可控。

概况的说：
*Docker 是为开发人员和系统管理员用于构建、发布、并运行分布式应用程序的开放式平台。该平台由 Docker 引擎（一个便携、轻巧的运行时和打包工具） 和 Docker Hub （一个共享应用程序和自动化工作流的云服务）等组成。Docker 可以使应用程序从组件迅速组装并消除了开发、质量保证和生产环境之间的摩擦问题。这样一来，IT部门可以更快地发布，而这些应用程序不管是运行在笔记本电脑、数据中心的虚拟机，还是任何的云，其运行过程和结果都是一致的。*

我们再来看下 Docker 的 Logo 。很明显，这是一只鲸鱼，它托着许多集装箱。我们可以把宿主机可当做这只鲸鱼，把相互隔离的容器可看成集装箱，每个集装箱中都包含自己的应用程序。这 Logo 简直的太形象了！

![](https://docs.docker.com/dist/assets/images/logo.png)

## Docker 的优点

* **轻量级**：所有容器在一台机器上共享同一个操作系统内核，这样他们立即开始，并更有效地利用内存。Image 是从分层文件系统的构建，这样他们能够共享公共文件，使得磁盘使用率和 Image 的下载更加高效。
* **开放**：Docker 容器是基于开发的标准，允许容器运行在主流的 Linux 发布版和 Microsoft 操作系统作为所有的基础设施。
* **安全**：容器使得应用程序彼此隔离，而基础架构同时为应用程序提供了额外的保护层。

## Docker 与 虚拟机的区别

容器与虚拟机有着类似的资源隔离和分配的优点，但不同的架构方法使容器能够更加便携，高效等。

### 虚拟机的架构

![](http://www.docker.com/sites/default/files/what-is-docker-diagram.png)

每个虚拟机都包括应用程序、必要的二进制文件和库以及一个完整的客户操作系统(Guest OS)，尽管它们被分离，它们共享并利用主机的硬件资源，将近需要十几个 GB 的大小。

### 容器的架构

![](http://www.docker.com/sites/default/files/what-is-vm-diagram.png)

容器包括应用程序及其所有的依赖，但与其他容器共享内核。它们以独立的用户空间进程形式运行在主机操作系统上。他们也不依赖于任何特定的基础设施，Docker 容器可以运行在任何计算机上，任何基础设施和任何云上。

Docker 的容器利用了 [LXC](https://linuxcontainers.org/)，管理利用了  namespaces 来做权限的控制和隔离，cgroups 来进行资源的配置，并且还通过  aufs 来进一步提高文件系统的资源利用率，而这些技术都不是 Docker 独创。

### LXC

LXC 与虚拟机的不同之处在于，它是一个操作系统级别的虚拟化环境，而不是硬件虚拟化环境。他们都做同样的事情，但 LXC 是操作系统级别的虚拟化环境，虚拟环境有它自己的进程和网络空间，而不是创建一个完整成熟的虚拟机。因此，一个 LXC 虚拟操作系统具有最小的资源需求，并启动只需几秒钟。

正如你可以在下图中看到的，左侧是 LXC 虚拟的 Ubuntu ，默认安装使用 11 MB 大小。

![](https://www.howtoforge.com/images/how-to-use-docker-introduction/big/pic_4.png)


## Docker 与 Microservices 的关系

Microservices（微服务） 依赖于“基础设施自动化”，而 Docker 正是“基础设施自动化”的利器。可以说 Docker 的火爆，一定程度上也带动了微服务架构的兴起，而微服务的广泛应用也促进了 Docker 繁荣。可以说两者相辅相成。

有关微服务的介绍，可以异步至《[简述 Microservices（微服务）](http://waylau.com/ahout-microservices/)》。

## 为啥要用 Docker

* **开发更加敏捷**：Docker 让开发人员可以自由定义环境，创建和部署的应用程序更快、更容易，IT 运维人员快速应对变化也更加灵活性。
* **更加可控**：Docker 使得开发人员保存从基础设施到应用的代码，帮助 IT 运维人管理拥有标准的、安全的、可扩展的操作环境。
* **高可移植性**：Docker 允许自由选择，可以是从笔记本电脑到一个团队，从私人基础设施到公共云提供商。

这样，你可以专注于开发应用，其他的繁琐事交给 Docker 去做吧。

## 如何使用 Docker

真是一个大话题，本文暂时不会详细涉及，会另外撰文介绍。如果对 Docker 抱有兴趣，可以参阅市面上的书籍：

* 《[The Docker Book](http://book.douban.com/subject/25931006/)》（James Turnbull）
* 《[Docker : Up and Running](http://book.douban.com/subject/26413766/) 》(Karl Matthias、Sean P. Kane)
* 《[Docker in Action](http://book.douban.com/subject/26409789/)》（Jeff Nickoloff）
* 《[Using docker](http://book.douban.com/subject/26423831/)》（Adrian Mouat ）

《[给程序员的开源、免费图书集合](https://github.com/waylau/books-collection)》收录了几本 Docker 免费的书籍有：

* 《[Docker —— 从入门到实践](https://github.com/yeasy/docker_practice)》
* 《[Docker Cheat Sheet](https://github.com/wsargent/docker-cheat-sheet)》
* 《[Docker 入门实战](http://yuedu.baidu.com/ebook/d817967416fc700abb68fca1)》
* 《[Docker 中文指南](https://github.com/widuu/chinese_docker)》

## 参考

* <http://www.docker.com/what-docker>
* <https://www.howtoforge.com/tutorial/how-to-use-docker-introduction/>
* <http://waylau.com/ahout-microservices>
* [给程序员的开源、免费图书集合](https://github.com/waylau/books-collection)