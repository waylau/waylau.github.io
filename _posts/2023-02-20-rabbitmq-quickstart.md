---
layout: post
title: RabbitMQ安装与使用
date: 2023-02-20 00:22
author: admin
comments: true
categories: [RabbitMQ]
tags: [RabbitMQ]
---

本文是RabbitMQ快速入门，介绍RabbitMQ安装与基本使用。

<!-- more -->


## RabbitMQ安装

下载地址：<https://www.rabbitmq.com/download.html>

该页面提供了各个操作系统的安装包，可以按需下载，比如本例在Windows环境下，可以使用Windows Installer “rabbitmq-server-3.11.9.exe”。



## 启动RabbitMQ

在RabbitMQ安装目录下（比如本例“D:\dev\java\rabbitmq\rabbitmq_server-3.11.8”）执行以下命令来启动RabbitMQ：


```
// 启动服务
sbin\rabbitmq-server start
```


### 访问RabbitMQ的控制台界面

可以通过以下命令开启Web插件

```
开启Web插件
sbin\rabbitmq-plugins enable rabbitmq_management 
```



可以通过<http:localhost:15672>来访问RabbitMQ的控制台界面。

* 账号：guest
* 密码：guest



## 关闭RabbitMQ

关闭RabbitMQ

```
sbin\rabbitmq-server stop
```


## 发送消息、接收消息

详细示例可以可见<https://github.com/waylau/distributed-systems-technologies-and-cases-analysis/tree/master/samples/chapter3/rabbitmq-demo>（Java版本）

当然，也可以在RabbitMQ的控制台界面来发送消息。


## 参考引用

* https://www.rabbitmq.com/documentation.html
* 原文同步至<https://waylau.com/rabbitmq-quickstart/>