---
layout: post
title: Apache RocketMQ安装与使用
date: 2023-02-13 00:22
author: admin
comments: true
categories: [RocketMQ]
tags: [RocketMQ]
---

本文是Apache RocketMQ快速入门，介绍RocketMQ安装与基本使用。

<!-- more -->

## RocketMQ安装

下载地址：<https://rocketmq.apache.org/download>

本例下载包为“rocketmq-all-5.0.0-bin-release.zip”，解压到任意的安装目录即可。



配置“ROCKETMQ_HOME”环境变量，值为解压目录。


## 启动服务器

Linux和Windows的命令相同，只不过一个是执行的.sh文件，另外一个执行的是.cmd文件。




启动NameServer

```
\bin>mqnamesrv

Java HotSpot(TM) 64-Bit Server VM warning: Using the DefNew young collector with the CMS collector is deprecated and will likely be removed in a future release
Java HotSpot(TM) 64-Bit Server VM warning: UseCMSCompactAtFullCollection is deprecated and will likely be removed in a future release.
The Name Server boot success. serializeType=JSON
```



启动Broker


```
\bin>mqbroker -n localhost:9876

The broker[waylau-lg, 10.10.52.63:10911] boot success. serializeType=JSON and name server is localhost:9876
```


## 发送消息、接收消息


tools工具自带了测试发送消息、接收消息的功能。


测试之前，需要配置“NAMESRV_ADDR”变量，值为“localhost:9876”


### 发送消息

命令如下：

```
\bin>tools org.apache.rocketmq.example.quickstart.Producer

SendResult [sendStatus=SEND_OK, msgId=7F00000167C0214C265E3FFA4D900000, offsetMsgId=0A0A343F00002A9F0000000000000000, messageQueue=MessageQueue [topic=TopicTest, brokerName=waylau-lg, queueId=2], queueOffset=0]

// ...省略中间数据

SendResult [sendStatus=SEND_OK, msgId=7F00000167C0214C265E3FFA54A703E7, offsetMsgId=0A0A343F00002A9F000000000003AC09, messageQueue=MessageQueue [topic=TopicTest, brokerName=waylau-lg, queueId=1], queueOffset=249]
10:09:30.296 [NettyClientSelector_1] INFO RocketmqRemoting - closeChannel: close the connection to remote address[127.0.0.1:9876] result: true
10:09:30.302 [NettyClientSelector_1] INFO RocketmqRemoting - closeChannel: close the connection to remote address[10.10.52.63:10911] result: true
```


该命令会发送1000条消息。


### 接收消息

命令如下：

```
\bin>tools org.apache.rocketmq.example.quickstart.Consumer

Consumer Started.
ConsumeMessageThread_please_rename_unique_group_name_4_7 Receive New Messages: [MessageExt [brokerName=waylau-lg, queueId=0, storeSize=239, queueOffset=0, sysFlag=0, bornTimestamp=1676254168686, bornHost=/10.10.52.63:1075, storeTimestamp=1676254168687, storeHost=/10.10.52.63:10911, msgId=0A0A343F00002A9F00000000000001DE, commitLogOffset=478, bodyCRC=1250039395, reconsumeTimes=0, preparedTransactionOffset=0, toString()=Message{topic='TopicTest', flag=0, properties={MIN_OFFSET=0, TRACE_ON=true, MAX_OFFSET=250, MSG_REGION=DefaultRegion, CONSUME_START_TIME=1676254461039, UNIQ_KEY=7F00000167C0214C265E3FFA4E6D0002, CLUSTER=DefaultCluster, WAIT=true, TAGS=TagA}, body=[72, 101, 108, 108, 111, 32, 82, 111, 99, 107, 101, 116, 77, 81, 32, 50], transactionId='null'}]]

// ...省略中间数据

ConsumeMessageThread_please_rename_unique_group_name_4_4 Receive New Messages: [MessageExt [brokerName=waylau-lg, queueId=2, storeSize=241, queueOffset=239, sysFlag=0, bornTimestamp=1676254170225, bornHost=/10.10.52.63:1075, storeTimestamp=1676254170226, storeHost=/10.10.52.63:10911, msgId=0A0A343F00002A9F000000000003838E, commitLogOffset=230286, bodyCRC=436465030, reconsumeTimes=0, preparedTransactionOffset=0, toString()=Message{topic='TopicTest', flag=0, properties={MIN_OFFSET=0, TRACE_ON=true, MAX_OFFSET=250, MSG_REGION=DefaultRegion, CONSUME_START_TIME=1676254461366, UNIQ_KEY=7F00000167C0214C265E3FFA547103BC, CLUSTER=DefaultCluster, WAIT=true, TAGS=TagA}, body=[72, 101, 108, 108, 111, 32, 82, 111, 99, 107, 101, 116, 77, 81, 32, 57, 53, 54], transactionId='null'}]]
```


该命令会消费1000条消息。



## 关闭服务器

```
\bin>mqshutdown broker

\bin>mqshutdown namesrv
```


## 参考引用

* https://rocketmq.apache.org/docs/quickStart/01quickstart
* 原文同步至<https://waylau.com/apache-rocketmq-quickstart/>