---
layout: post
title: 程序性能的两种确定方式
date: 2021-12-20 00:22
author: admin
comments: true
categories: [Java数据结构及算法实战]
tags: [Java,Algorithm]
---

本节是《Java数据结构及算法实战》系列的第4节，主要介绍程确定序性能的两种方式。

<!-- more -->

可以采用两种方法来确定一个程序的性能，一种是实验的方法，另外一种是分析的方法。


## 1. 采用实验的方式来确定程序的性能

在实际的工作中，采用实验的方式来测试程序的性能通常就是指**性能测试**（Performance Testing）。性能测试是通过自动化的测试工具模拟多种正常、峰值以及异常负载条件来对系统的各项性能指标进行测试。负载测试和压力测试都属于性能测试，两者可以结合进行。通过负载测试，确定在各种工作负载下系统的性能，目标是测试当负载逐渐增加时，系统各项性能指标的变化情况。压力测试是通过确定一个系统的瓶颈或者不能接受的性能点，来获得系统能提供的最大服务级别的测试。

举一个JDK性能测试的例子，该测试是通过在被测试的每个发行版上使用相同的Java字节码编译的Java程序来查看GC（垃圾回收器）的性能。为了保证测试受干扰的因素将降至最低，每次都使用相同的选项。每个发行版选取了最具代表性的三个版本，即JDK 8、JDK 14和JDK 15。性能测试结果如图1-4所示（数据越大性能越好）<sup>[1]</sup>。



![](../images/post/20211220-g1-ootb.png)




## 2. 采用分析的方式来确定程序的性能

虽然采用实验的方式所获取测试数据，更接近实际程序的性能情况，但并非所有的场景都能采用实验的方式。比如，

* 正在开发的一个新算法，这个算法还未正式开发完成；
* 测试资源有限，并非任何人都具备性能测试的环境。

在上述的情况之下，使用分析的方法来确定程序的性能。所谓分析的方法，就是通过阅读程序代码，分析程序所使用的算法，来估算出程序的性能情况。

在本书，主要是采用分析的方式来确定程序的性能。




<sub>[1]数据来源<https://kstefanj.github.io/2020/04/16/g1-ootb-performance.html></sub>

## 参考引用

* 原本同步至：<https://waylau.com/two-ways-to-determine-program-performance/>
* 本系列归档：<https://github.com/waylau/java-data-structures-and-algorithms-in-action>
* 数据结构和算法基础（Java语言实现）：<https://item.jd.com/13014179.html>