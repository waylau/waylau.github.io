---
layout: post
title: Apache ActiveMQ Artemis安装与使用
date: 2023-02-14 00:22
author: admin
comments: true
categories: [ActiveMQ]
tags: [ActiveMQ, Artemis]
---

本文是Apache ActiveMQ Artemis快速入门，介绍ActiveMQ Artemis安装与基本使用。

<!-- more -->

## ActiveMQ Artemis安装

下载地址：<https://activemq.apache.org/components/artemis/download/>

本例下载包为“apache-artemis-2.28.0-bin.zip”，解压到任意的安装目录即可。



配置“ARTEMIS_HOME”环境变量，值为解压目录。


## 创建 Broker 实例


Broker实例是包含与Broker进程相关联的所有配置和运行时数据（如日志和消息日志）的目录。建议不要在`${ARTEMIS_HOME}`下创建实例目录。

比如本例，指定的Broker实例目录为“D:\data\activemq\artemis”。

在指定的Broker实例目录下执行以下命令进行安装：


```
cd /var/lib
${ARTEMIS_HOME}/bin/artemis create mybroker
```

Windows下命令如下：

```
cd D:\data\activemq\artemis
D:
%ARTEMIS_HOME%/bin/artemis create mybroker
```

其中，“mybroker”可以是自定义的Broker实例的名称。

示例是完整的操作过程如下：


```
C:\Users\wayla>cd D:\data\activemq\artemis

C:\Users\wayla>d:

D:\data\activemq\artemis>%ARTEMIS_HOME%/bin/artemis create mybroker
Creating ActiveMQ Artemis instance at: D:\data\activemq\artemis\mybroker

--user:
What is the default username?
admin

--password: is mandatory with this configuration:
What is the default password?


--allow-anonymous | --require-login:
Allow anonymous access?, valid values are Y, N, True, False
y

Auto tuning journal ...
done! Your system can make 0.4 writes per millisecond, your journal-buffer-timeout will be 2496000

You can now start the broker by executing:

   "D:\data\activemq\artemis\mybroker\bin\artemis" run

Or you can setup the broker as Windows service and run it in the background:

   "D:\data\activemq\artemis\mybroker\bin\artemis-service.exe" install
   "D:\data\activemq\artemis\mybroker\bin\artemis-service.exe" start

   To stop the windows service:
      "D:\data\activemq\artemis\mybroker\bin\artemis-service.exe" stop

   To uninstall the windows service
      "D:\data\activemq\artemis\mybroker\bin\artemis-service.exe" uninstall
```

上述安装过程中会要求输入用户名和密码，本例为用户名“admin”密码为“123456”


## 启动、关闭Broker实例

Broker实例“mybroker”的安装目录是在“D:\data\activemq\artemis\mybroker”，在该目录的bin目录下有可执行文件artemis.cmd。




### 启动Broker实例

启动Broker实例

```
artemis run
```


启动之后，可以看到控制台输出内容如下：

```
D:\data\activemq\artemis\mybroker\bin>artemis run
     _        _               _
    / \  ____| |_  ___ __  __(_) _____
   / _ \|  _ \ __|/ _ \  \/  | |/  __/
  / ___ \ | \/ |_/  __/ |\/| | |\___ \
 /_/   \_\|   \__\____|_|  |_|_|/___ /
 Apache ActiveMQ Artemis 2.28.0


2023-02-14 09:32:38,354 INFO  [org.apache.activemq.artemis.integration.bootstrap] AMQ101000: Starting ActiveMQ Artemis Server version 2.28.0
2023-02-14 09:32:38,476 INFO  [org.apache.activemq.artemis.core.server] AMQ221000: live Message Broker is starting with configuration Broker Configuration (clustered=false,journalDirectory=data/journal,bindingsDirectory=data/bindings,largeMessagesDirectory=data/large-messages,pagingDirectory=data/paging)
2023-02-14 09:32:38,502 INFO  [org.apache.activemq.artemis.core.server] AMQ221013: Using NIO Journal
2023-02-14 09:32:38,544 INFO  [org.apache.activemq.artemis.core.server] AMQ221057: Global Max Size is being adjusted to 1/2 of the JVM max size (-Xmx). being defined as 1073741824
2023-02-14 09:32:38,566 INFO  [org.apache.activemq.artemis.core.server] AMQ221043: Protocol module found: [artemis-server]. Adding protocol support for: CORE
2023-02-14 09:32:38,566 INFO  [org.apache.activemq.artemis.core.server] AMQ221043: Protocol module found: [artemis-amqp-protocol]. Adding protocol support for: AMQP
2023-02-14 09:32:38,567 INFO  [org.apache.activemq.artemis.core.server] AMQ221043: Protocol module found: [artemis-hornetq-protocol]. Adding protocol support for: HORNETQ
2023-02-14 09:32:38,567 INFO  [org.apache.activemq.artemis.core.server] AMQ221043: Protocol module found: [artemis-mqtt-protocol]. Adding protocol support for: MQTT
2023-02-14 09:32:38,568 INFO  [org.apache.activemq.artemis.core.server] AMQ221043: Protocol module found: [artemis-openwire-protocol]. Adding protocol support for: OPENWIRE
2023-02-14 09:32:38,568 INFO  [org.apache.activemq.artemis.core.server] AMQ221043: Protocol module found: [artemis-stomp-protocol]. Adding protocol support for: STOMP
2023-02-14 09:32:38,616 INFO  [org.apache.activemq.artemis.core.server] AMQ221034: Waiting indefinitely to obtain live lock
2023-02-14 09:32:38,616 INFO  [org.apache.activemq.artemis.core.server] AMQ221035: Live Server Obtained live lock
2023-02-14 09:32:38,717 INFO  [org.apache.activemq.artemis.core.server] AMQ221080: Deploying address DLQ supporting [ANYCAST]
2023-02-14 09:32:38,727 INFO  [org.apache.activemq.artemis.core.server] AMQ221003: Deploying ANYCAST queue DLQ on address DLQ
2023-02-14 09:32:38,765 INFO  [org.apache.activemq.artemis.core.server] AMQ221080: Deploying address ExpiryQueue supporting [ANYCAST]
2023-02-14 09:32:38,767 INFO  [org.apache.activemq.artemis.core.server] AMQ221003: Deploying ANYCAST queue ExpiryQueue on address ExpiryQueue
2023-02-14 09:32:39,156 INFO  [org.apache.activemq.artemis.core.server] AMQ221020: Started NIO Acceptor at 0.0.0.0:61616 for protocols [CORE,MQTT,AMQP,STOMP,HORNETQ,OPENWIRE]
2023-02-14 09:32:39,186 INFO  [org.apache.activemq.artemis.core.server] AMQ221020: Started NIO Acceptor at 0.0.0.0:5445 for protocols [HORNETQ,STOMP]
2023-02-14 09:32:39,213 INFO  [org.apache.activemq.artemis.core.server] AMQ221020: Started NIO Acceptor at 0.0.0.0:5672 for protocols [AMQP]
2023-02-14 09:32:39,235 INFO  [org.apache.activemq.artemis.core.server] AMQ221020: Started NIO Acceptor at 0.0.0.0:1883 for protocols [MQTT]
2023-02-14 09:32:39,258 INFO  [org.apache.activemq.artemis.core.server] AMQ221020: Started NIO Acceptor at 0.0.0.0:61613 for protocols [STOMP]
2023-02-14 09:32:39,264 INFO  [org.apache.activemq.artemis.core.server] AMQ221007: Server is now live
2023-02-14 09:32:39,264 INFO  [org.apache.activemq.artemis.core.server] AMQ221001: Apache ActiveMQ Artemis Message Broker version 2.28.0 [0.0.0.0, nodeID=774d5b12-ac07-11ed-8248-78af08fe9de0]
2023-02-14 09:32:39,270 INFO  [org.apache.activemq.artemis] AMQ241003: Starting embedded web server
2023-02-14 09:32:39,560 INFO  [org.apache.activemq.hawtio.branding.PluginContextListener] Initialized activemq-branding plugin
2023-02-14 09:32:39,634 INFO  [org.apache.activemq.hawtio.plugin.PluginContextListener] Initialized artemis-plugin plugin
2023-02-14 09:32:40,781 INFO  [io.hawt.HawtioContextListener] Initialising hawtio services
2023-02-14 09:32:40,786 INFO  [io.hawt.system.ConfigManager] Configuration will be discovered via system properties
2023-02-14 09:32:40,788 INFO  [io.hawt.jmx.JmxTreeWatcher] Welcome to Hawtio 2.15.0
2023-02-14 09:32:40,793 INFO  [io.hawt.web.auth.AuthenticationConfiguration] Starting hawtio authentication filter, JAAS realm: "activemq" authorized role(s): "amq" role principal classes: "org.apache.activemq.artemis.spi.core.security.jaas.RolePrincipal"
2023-02-14 09:32:40,798 INFO  [io.hawt.web.auth.LoginRedirectFilter] Hawtio loginRedirectFilter is using 1800 sec. HttpSession timeout
2023-02-14 09:32:40,804 INFO  [io.hawt.web.proxy.ProxyServlet] Proxy servlet is disabled
2023-02-14 09:32:40,812 INFO  [io.hawt.web.servlets.JolokiaConfiguredAgentServlet] Jolokia overridden property: [key=policyLocation, value=file:/D:/data/activemq/artemis/mybroker/etc/\jolokia-access.xml]
2023-02-14 09:32:40,896 INFO  [org.apache.activemq.artemis] AMQ241001: HTTP Server started at http://localhost:8161
2023-02-14 09:32:40,897 INFO  [org.apache.activemq.artemis] AMQ241002: Artemis Jolokia REST API available at http://localhost:8161/console/jolokia
2023-02-14 09:32:40,897 INFO  [org.apache.activemq.artemis] AMQ241004: Artemis Console available at http://localhost:8161/console
```

### 访问Artemis的控制台界面

可以通过<http://localhost:8161>来访问Artemis的控制台界面。

### 关闭Broker实例

关闭Broker实例

```
artemis stop
```


## 发送消息、接收消息


在`${ARTEMIS_HOME}\examples`目录下，自带了非常多的使用示例，可以选取其中的示例来验证发送消息、接收消息功能。


比如，选取了“examples\features\standard\queue”这个例子，在Broker实例启动的情况下，执行以下命令来运行示例

```
mvn -PnoServer verify
```


或者在没有Broker实例启动的情况下，执行以下命令来运行示例

```
mvn verify
```


运行完成之后，可以看到控制台输出内容如下;


```
[INFO] --- artemis-maven-plugin:2.28.0:cli (start) @ queue ---
[INFO]
[INFO] --- artemis-maven-plugin:2.28.0:runClient (runClient) @ queue ---
Sent message: This is a text message
Received message: This is a text message
[INFO]
[INFO] --- artemis-maven-plugin:2.28.0:cli (stop) @ queue ---
[INFO] ------------------------------------------------------------------------
```

可以看到示例程序已经能够成功发送和接收到消息“This is a text message”了。

## 参考引用

* hhttps://activemq.apache.org/components/artemis/documentation/
* 原文同步至<https://waylau.com/apache-activemq-quickstart/>