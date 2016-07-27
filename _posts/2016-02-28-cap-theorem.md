---
layout: post
title: 浅谈 CAP 理论
date: 2016-02-28 02:41
author: admin
comments: true
categories: [CAP,Distributed]
tags: [CAP,Distributed,分布式,架构]
---
本文介绍了介绍了分布式系统著名的 CAP 理论。什么是 CAP 理论？为什么说 CAP 只能三选二？了解 CAP 对于系统架构又有什么指导意义？本文将一一作答。

<!-- more -->

## 什么是 CAP 理论

![图1 CAP 定理](/images/post/20160228-cap-theorem-001.jpg)



在计算机科学理论，CAP 定理（也称为 Brewer 定理），是由计算机科学家 Eric Brewer 提出的，即在分布式计算机系统不可能同时提供以下全部三个保证：

* 一致性（Consistency）：所有节点同一时间看到是相同的数据；
* 可用性（Availability）：不管是否成功，确保每一个请求都能接收到响应；
* 分区容错性（Partition tolerance）：系统任意分区后，在网络故障时，仍能操作

## 为什么说 CAP 只能三选二

下面分别举例说明了为什么说  CAP 只能三选二。

![](http://www.julianbrowne.com/assets/attachments/brewers-cap-theorem/images/intro.png)


上面的图显示了在一个网络中，N1 和 N2 两个节点。他们都共享数据块 V，其中有一个值 V0 。运行在 N1 的 A 程序可以认为是安全的、无 bug、可预测的和可靠的。运行在 N2 是 B 程序。这个例子中，A 将写入 V 新​值，而 B 从 V 读取值

![](http://www.julianbrowne.com/assets/attachments/brewers-cap-theorem/images/scenario-1.png)


系统预期执行下面的操作

1. 首先写一个 V 的新​值 V1
2. 然后消息（M）从 N1 更新 V 的拷贝到 N2
3. 现在，从 B 读取将返回 V1

![](http://www.julianbrowne.com/assets/attachments/brewers-cap-theorem/images/scenario-2.png)


如果网络是分区的，当 N1 到 N2 的消息不能传递时，执行上面的第三步，会出现虽然 N2 能访问到 V 的值（可用性），但其实与 N1 的 V 的值已经不一致了（一致性）。

## CAP 常见模型

### 牺牲分区（CA 模型）

![图 CAP 定理](/images/post/20160228-cap-theorem-002.jpg)


举例：

* 单站点数据库
* 集群数据库
* LDAP
* xFS 文件系统

实现方式：

* 两阶段提交
* 缓存验证协议

### 牺牲可用性（CP 模型）

![图 CAP 定理](/images/post/20160228-cap-theorem-003.jpg)


举例：

* 分布式数据库
* 分布式锁定
* 绝大部分协议

实现方式：

* 悲观锁
* 少数分区不可用

### 牺牲一致性（AP 模型）

![图 CAP 定理](/images/post/20160228-cap-theorem-004.jpg)


举例：

* Coda
* Web 缓存
* DNS

实现方式：

* 到期/租赁
* 解决冲突
* 乐观

## CAP 的意义

在系统架构时，应该根据具体的业务场景，来权衡 CAP。比如，对于大多数互联网应用来说（如门户网站），因为机器数量庞大，部署节点分散，网络故障是常态，可用性是必须需要保证的，所以只有舍弃一致性来保证服务的 AP。而对于银行等，需要确保一致性的场景，通常会权衡 CA 和 CP 模型，CA 模型网络故障时完全不可用，CP 模型具备部分可用性。

## 参考引用

* Eric Brewer, ["Towards Robust Distributed Systems"](http://www.cs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)
* ["Brewer's CAP Theorem"](http://www.julianbrowne.com/article/viewer/brewers-cap-theorem), julianbrowne.com, Retrieved 02-Mar-2010
