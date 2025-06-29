---
layout: post
title: Apache Kafka 4.0在Windows下的安装与使用
date: 2025-07-01 00:22
author: admin
comments: true
categories: [Kafka]
tags: [Kafka]
---

在往期文章《[Apache Kafka安装与使用](https://waylau.com/apache-kafka-quickstart/)》介绍了Apache Kafka 3.4.0快速入门。自从Kafka 4.0发布，相关的安装、配置发生了差异。本文主要介绍pache Kafka 4.0在Windows下的安装与使用。

<!-- more -->

## 下载 Kafka


访问 [Apache Kafka 官网](https://kafka.apache.org/downloads)，选择 **Binary Downloads**，下载 `kafka_2.13-4.0.0.tgz`（支持 Windows/Linux）。

## 依赖项

Kafka 4.0 基于 JDK 17 编译，需提前安装并配置 `JAVA_HOME` 环境变量。 
 

## 安装


将压缩包解压至短路径目录（如 `D:\dev\java\kafka_2.13-4.0.0`），避免路径过长导致错误。

## 配置日志目录


创建 Kafka 日志目录（如 `D:\data\kafka\kafka-logs`）。

## 修改配置文件


打开 `config\server.properties`，修改以下关键参数：  

```properties
# 日志路径（使用双反斜杠）
log.dirs=D:\\data\\kafka\\kafka-logs
```

## 初始化存储目录


生成集群 ID：  

```cmd
cd D:\D:\dev\java\kafka_2.13-4.0.0\bin\windows
kafka-storage.bat random-uuid
```

输出示例如下：

```
2025-06-29T09:18:15.572495900Z main ERROR Reconfiguration failed: No configuration found for '7852e922' at 'null' in 'null'
4LBwTZK_QhCTbsmqDPw4lw
```

上述日志中有个错误提示可以忽略。记录此ID：`4LBwTZK_QhCTbsmqDPw4lw`

打开 `config\server.properties`，增加以下关键参数：  

```properties

# 集群唯一标识（之前生成的 UUID）
cluster.id=4LBwTZK_QhCTbsmqDPw4lw
 
# 控制器选举配置（单机模式示例）
controller.quorum.voters=1@localhost:9093
```

Kafka 4.0 默认使用 KRaft 模式（Kafka Raft Metadata），取代了旧版的 ZooKeeper。在 KRaft 模式下，`controller.quorum.voters` 是必需配置，用于指定集群的控制器节点。


格式化日志目录：  

```cmd
kafka-storage.bat format -t 4LBwTZK_QhCTbsmqDPw4lw -c ..\..\config\server.properties
```


验证：检查 `D:\data\kafka\kafka-logs` 目录下是否生成 `meta.properties` 和 `bootstrap.checkpoint` 文件。

## 启动 Kafka


在 `bin\windows` 目录下执行：  

```cmd
kafka-server-start.bat ..\..\config\server.properties
```

成功标志：日志输出如下内容且无致命错误：


```
[2025-06-29 17:35:20,993] INFO Kafka version: 4.0.0 (org.apache.kafka.common.utils.AppInfoParser)
[2025-06-29 17:35:20,993] INFO Kafka commitId: 985bc99521dd22bb (org.apache.kafka.common.utils.AppInfoParser)
[2025-06-29 17:35:20,993] INFO Kafka startTimeMs: 1751189720992 (org.apache.kafka.common.utils.AppInfoParser)
[2025-06-29 17:35:20,995] INFO [KafkaRaftServer nodeId=1] Kafka Server started (kafka.server.KafkaRaftServer)
```



## 参考引用


* 原文同步至<https://waylau.com/installation-and-use-of-apache-kafka-4-on-windows/>
* 《跟老卫学Apache Kafka开发》 <https://github.com/waylau/apache-kafka-tutorial>
* 《循序渐进Spark大数据应用开发》(清华大学出版社2024年11月出版) <https://waylau.com/about-spark-tutorial-book/>