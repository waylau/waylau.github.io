---
layout: post
title: Apache Kafka安装与使用
date: 2023-02-09 00:22
author: admin
comments: true
categories: [Kafka]
tags: [Kafka]
---

本文是Apache Kafka快速入门，介绍Kafka安装与基本使用。

<!-- more -->

## 下载 Kafka


访问 <https://kafka.apache.org/downloads> 下载Kafka的安装包，比如本例是“kafka_2.13-3.4.0.tgz”，解压到安装位置即可。


## 启动Kafka环境

注意：地环境必须安装Java 8+。


Apache Kafka可以使用ZooKeeper或KRaft启动，要开始使用任何一种配置，请遵循以下配置，但不要同时使用这两个配置。



### 启用ZooKeeper配置

运行以下命令以正确顺序启动所有服务：

```
#启动ZooKeeper服务
$ bin/zookeeper-server-start.sh config/zookeeper.properties
```

其中，可以在zookeeper.properties文件中自定义数据存放的目录：

```
#dataDir=/tmp/zookeeper
dataDir=D:/data/zookeeper
```



打开另一个终端会话并运行：

```
#启动Kafka代理服务
$ bin/kafka-server-start.sh config/server.properties
```

其中，可以在server.properties文件中自定义日志存放的目录：

```
#log.dirs=/tmp/kafka-logs
log.dirs=D:/data/kafka/kafka-logs
```

一旦所有服务都成功启动，将有一个基本的Kafka环境正在运行并随时可以使用。


**注**：上述命令是在Linux下执行，如果是Windows环境下，则要执行windows下的目录，例如`bin/windows/*.bat`脚本。
命令如下：

```
cd bin/windows

zookeeper-server-start.bat ../../config/zookeeper.properties

kafka-server-start.bat ../../config/server.properties
```


### 启用KRaft配置

生成群集UUID

```
$ KAFKA_CLUSTER_ID="$(bin/kafka-storage.sh random-uuid)"
```


设置日志目录格式

```
$ bin/kafka-storage.sh format -t $KAFKA_CLUSTER_ID -c config/kraft/server.properties
```


启动Kafka服务器

```
$ bin/kafka-server-start.sh config/kraft/server.properties
```


一旦Kafka服务器成功启动，将有一个基本的Kafka环境正在运行并可以使用。


## 创建主题（topic）以存储事件（event）

Kafka是一个分布式事件流平台，它允许在多台机器上读、写、存储和处理事件（也称为记录或消息）。



示例事件包括支付交易、移动电话的地理位置更新、发货订单、物联网设备或医疗设备的传感器测量等。这些事件按主题组织和存储。

主题类似于文件系统中的文件夹，事件是该文件夹中的文件。

因此，在撰写第一个事件之前，必须创建一个主题。打开另一个终端会话并运行：

```
$ bin/kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092
```


Kafka的所有命令行工具都有其他选项。例如，它还可以显示新主题的分区计数等详细信息：


```
$ bin/kafka-topics.sh --describe --topic quickstart-events --bootstrap-server localhost:9092
Topic: quickstart-events        TopicId: NPmZHyhbR9y00wMglMH2sg PartitionCount: 1       ReplicationFactor: 1	Configs:
    Topic: quickstart-events Partition: 0    Leader: 0   Replicas: 0 Isr: 0
```

## 写事件到主题


Kafka客户端通过网络与Kafka代理进行通信，以编写（或阅读）事件。一旦收到，代理将以持久和容错的方式存储事件。

运行控制台生产者客户端，将一些事件写入主题。默认情况下，输入的每一行都将导致一个单独的事件写入主题。


```
$ bin/kafka-console-producer.sh --topic quickstart-events --bootstrap-server localhost:9092
This is my first event
This is my second event
```



可以随时使用Ctrl-C停止生产者客户端。


## 从主题读事件

打开另一个终端会话并运行控制台使用者客户端以读取刚刚创建的事件：


```
$ bin/kafka-console-consumer.sh --topic quickstart-events --from-beginning --bootstrap-server localhost:9092
This is my first event
This is my second event
```


可以随时使用Ctrl-C停止消费者客户端。

## Windows下启用KRaft配置报错


报错如下：

```
java.io.UncheckedIOException: Error while writing the Quorum status from the file D:\data\kafka\kraft-combined-logs\__cluster_metadata-0\quorum-state
        at org.apache.kafka.raft.FileBasedStateStore.writeElectionStateToFile(FileBasedStateStore.java:155)
        at org.apache.kafka.raft.FileBasedStateStore.writeElectionState(FileBasedStateStore.java:128)
        at org.apache.kafka.raft.QuorumState.transitionTo(QuorumState.java:477)
        at org.apache.kafka.raft.QuorumState.initialize(QuorumState.java:212)
        at org.apache.kafka.raft.KafkaRaftClient.initialize(KafkaRaftClient.java:369)
        at kafka.raft.KafkaRaftManager.buildRaftClient(RaftManager.scala:240)
        at kafka.raft.KafkaRaftManager.<init>(RaftManager.scala:166)
        at kafka.server.SharedServer.start(SharedServer.scala:228)
        at kafka.server.SharedServer.startForController(SharedServer.scala:128)
        at kafka.server.ControllerServer.startup(ControllerServer.scala:188)
        at kafka.server.KafkaRaftServer.$anonfun$startup$1(KafkaRaftServer.scala:98)
        at kafka.server.KafkaRaftServer.$anonfun$startup$1$adapted(KafkaRaftServer.scala:98)
        at scala.Option.foreach(Option.scala:437)
        at kafka.server.KafkaRaftServer.startup(KafkaRaftServer.scala:98)
        at kafka.Kafka$.main(Kafka.scala:115)
        at kafka.Kafka.main(Kafka.scala)
Caused by: java.nio.file.FileSystemException: D:\data\kafka\kraft-combined-logs\__cluster_metadata-0\quorum-state.tmp -> D:\data\kafka\kraft-combined-logs\__cluster_metadata-0\quorum-state: 另一个程序正在使用此文件，进程无法访问。

        at sun.nio.fs.WindowsException.translateToIOException(WindowsException.java:86)
        at sun.nio.fs.WindowsException.rethrowAsIOException(WindowsException.java:97)
        at sun.nio.fs.WindowsFileCopy.move(WindowsFileCopy.java:387)
        at sun.nio.fs.WindowsFileSystemProvider.move(WindowsFileSystemProvider.java:287)
        at java.nio.file.Files.move(Files.java:1395)
        at org.apache.kafka.common.utils.Utils.atomicMoveWithFallback(Utils.java:949)
        at org.apache.kafka.common.utils.Utils.atomicMoveWithFallback(Utils.java:932)
        at org.apache.kafka.raft.FileBasedStateStore.writeElectionStateToFile(FileBasedStateStore.java:152)
        ... 15 more
```

该问题暂时还没有解决，在GitHub上已经有人反馈了<https://github.com/apache/kafka/pull/12763>


## 参考引用

* https://kafka.apache.org/quickstart
* 原文同步至<https://waylau.com/apache-kafka-quickstart/>