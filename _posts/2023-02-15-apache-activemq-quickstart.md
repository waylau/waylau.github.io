---
layout: post
title: Apache ActiveMQ安装与使用
date: 2023-02-15 00:22
author: admin
comments: true
categories: [ActiveMQ]
tags: [ActiveMQ]
---

本文是Apache ActiveMQ快速入门，介绍ActiveMQ安装与基本使用。

<!-- more -->
## ActiveMQ的分支

ActiveMQ分为了Classic和Artemis两个分支。

如果没有特殊说明，ActiveMQ就是指传统的分支Classic。

有关ActiveMQ Artemis详见<https://waylau.com/apache-activemq-artemis-quickstart/>

## ActiveMQ安装

下载地址：<https://activemq.apache.org/components/classic/download/>

本例下载包为“	apache-activemq-5.17.3-bin.zip”，解压到任意的安装目录即可。



## 启动ActiveMQ

在ActiveMQ安装目录下（比如本例“D:\dev\java\apache-activemq-5.17.3”）执行以下命令来启动ActiveMQ：


```
bin\activemq start
```


### 访问ActiveMQ的控制台界面

可以通过<http://localhost:8161/admin/>来访问ActiveMQ的控制台界面。

* 账号：admin
* 密码：admin



## 关闭ActiveMQ

关闭ActiveMQ

```
bin\activemq stop
```


## 发送消息、接收消息


以下是发送消息的示例：

```
bin\activemq producer --message "My message" --messageCount 1
```



以下是接收消息的示例：

```
bin\activemq consumer
```

控制台输出如下：

```
D:\dev\java\apache-activemq-5.17.3>bin\activemq consumer
Java Runtime: Oracle Corporation 19.0.2 D:\dev\java\openjdk-19.0.2_windows-x64_bin\jdk-19.0.2
  Heap sizes: current=1048576k  free=1037822k  max=1048576k
    JVM args: -Dcom.sun.management.jmxremote -Xms1G -Xmx1G -Djava.util.logging.config.file=logging.properties -Djava.security.auth.login.config=D:\dev\java\apache-activemq-5.17.3\bin\..\conf\login.config -Dactivemq.classpath=D:\dev\java\apache-activemq-5.17.3\bin\..\conf;D:\dev\java\apache-activemq-5.17.3\bin\../conf;D:\dev\java\apache-activemq-5.17.3\bin\../conf; -Dactivemq.home=D:\dev\java\apache-activemq-5.17.3\bin\.. -Dactivemq.base=D:\dev\java\apache-activemq-5.17.3\bin\.. -Dactivemq.conf=D:\dev\java\apache-activemq-5.17.3\bin\..\conf -Dactivemq.data=D:\dev\java\apache-activemq-5.17.3\bin\..\data -Djolokia.conf=file:D:\\dev\\java\\apache-activemq-5.17.3\\bin\\..\\conf\\jolokia-access.xml -Djava.io.tmpdir=D:\dev\java\apache-activemq-5.17.3\bin\..\data\tmp
Extensions classpath:
  [D:\dev\java\apache-activemq-5.17.3\bin\..\lib,D:\dev\java\apache-activemq-5.17.3\bin\..\lib\camel,D:\dev\java\apache-activemq-5.17.3\bin\..\lib\optional,D:\dev\java\apache-activemq-5.17.3\bin\..\lib\web,D:\dev\java\apache-activemq-5.17.3\bin\..\lib\extra]
ACTIVEMQ_HOME: D:\dev\java\apache-activemq-5.17.3\bin\..
ACTIVEMQ_BASE: D:\dev\java\apache-activemq-5.17.3\bin\..
ACTIVEMQ_CONF: D:\dev\java\apache-activemq-5.17.3\bin\..\conf
ACTIVEMQ_DATA: D:\dev\java\apache-activemq-5.17.3\bin\..\data
 INFO | Connecting to URL: failover://tcp://localhost:61616 as user: null
 INFO | Consuming queue://TEST
 INFO | Sleeping between receives 0 ms
 INFO | Running 1 parallel threads
 INFO | Successfully connected to tcp://localhost:61616
 INFO | consumer-1 wait until 1000 messages are consumed
 INFO | consumer-1 Received My message
 ```


 可以看到已经接收到消息“My message”。


 当然，也可以在ActiveMQ的控制台界面来发送消息。


## 参考引用

* https://activemq.apache.org/components/classic/documentation
* 原文同步至<https://waylau.com/apache-activemq-quickstart/>