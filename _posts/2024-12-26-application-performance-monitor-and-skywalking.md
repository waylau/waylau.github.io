---
layout: post
title: 使用SkyWalking实现应用性能监控
date: 2024-12-26 00:22
author: admin
comments: true
categories: [SkyWalking]
tags: [SkyWalking, APM]
---



应用性能监控（Application Performance Monitor，简称APM），提供应用性能监控管理、微服务监控、K8S监控等服务，透视性能瓶颈，追踪问题根源，提升用户体验。本文介绍了常见的APM产品、选型原因及应用接入方式。

<!-- more -->

## 一、常见的APM产品

目前市面上常用的开源APM产品主要是SkyWalking和Prometheus。


### SkyWalking

SkyWalking是一个开源的应用性能监控（APM）系统，尤其针对分布式系统的微服务架构。

功能特点：

* 性能监控：SkyWalking可以收集应用程序的运行时数据，包括调用链、响应时间、吞吐量等指标，帮助用户了解应用程序的性能状况。
* 服务拓扑图：SkyWalking可以根据收集到的数据生成服务拓扑图，展示服务之间的依赖关系和调用关系，方便用户进行故障排查和性能优化。
* 追踪分析：SkyWalking支持对分布式调用链进行追踪分析，可以帮助用户快速定位性能瓶颈和故障点。
* 告警管理：SkyWalking提供了告警管理功能，可以根据预设的规则和阈值对异常情况进行告警，帮助用户及时发现和解决问题。

技术架构：

* SkyWalking采用了分布式的架构设计，通过多个组件共同协作来实现监控功能。主要组件包括Collector（负责收集应用程序的性能数据）、Storage（负责存储收集到的性能数据）、Analysis（负责对收集到的性能数据进行分析和处理）以及UI（提供用户界面，展示性能数据和服务拓扑图等信息）。

使用场景：

* SkyWalking主要用于监控分布式系统的性能问题，特别适用于微服务架构和云原生应用的监控。它可以提供详细的调用链信息和性能指标，帮助用户快速定位性能瓶颈和故障点。



### Prometheus

Prometheus是一个开源的监控系统和时间序列数据库，以下是对Prometheus的详细评价：

功能特点：

* 指标收集：Prometheus可以通过配置监控目标来收集各种指标数据，包括系统资源使用情况、应用程序性能指标等。
* 时序数据库：Prometheus将收集到的指标数据存储在时序数据库中，支持高效的数据存储和查询。
* 数据可视化：Prometheus提供了丰富的数据可视化工具（如Grafana），可以将收集到的指标数据以图表的形式展示出来，帮助用户直观地了解系统运行状况。
* 告警管理：Prometheus支持基于规则的告警管理，可以根据预设的条件和阈值对异常情况进行告警，帮助用户及时发现和解决问题。

技术架构：

* Prometheus采用了单节点的架构设计，每个Prometheus实例都是独立的，通过HTTP协议进行通信。主要组件包括Prometheus Server（负责收集指标数据，并提供服务发现和告警管理等功能）、Exporters（负责从其他系统或应用程序中收集指标数据，并将其暴露给Prometheus Server）、Client Library（提供客户端库，用于在应用程序中嵌入指标数据采集逻辑）以及Alertmanager（负责管理和处理告警信息，支持多种告警方式和通知渠道）。

使用场景：

* Prometheus主要用于收集和存储各种类型的指标数据，特别适用于系统资源使用情况的监控和度量。它可以提供高效的数据存储和查询能力，支持自定义指标和告警规则，帮助用户了解系统的运行状况和健康状况。

### 综合对比


* 功能侧重点：SkyWalking更侧重于分布式系统的性能监控和追踪分析，而Prometheus则更侧重于系统资源使用情况的监控和度量。
* 架构差异：SkyWalking采用分布式架构设计，适用于大规模分布式系统的监控；而Prometheus采用单节点架构设计，易于部署和管理。
* 可视化与告警：两者都提供了丰富的可视化工具和告警管理功能，但SkyWalking在分布式追踪和性能监控方面的可视化展示更具优势。
* 侵入性：SkyWalking埋点无侵入，已加入Apache孵化器，遵循OpenTelemetry规范，探针性能对被接入的应用系统吞吐量影响小。


综上，SkyWalking比较符合咱们产品的应用场景。


## 二、应用接入方式


SkyWalking平台管理界面地址为：<http://192.168.1.86:19000/>

应用接入SkyWalking平台的方式分为Java项目接入和Node.js项目接入。接入完成之后，可以在上述管理界面进行查看应用信息。



### Java项目接入


下载 Apache SkyWalking的Java Agent，见FTP服务器的apache-skywalking-java-agent-9.3.0.tgz文件。


把压缩包 apache-skywalking-java-agent-9.3.0.tgz 拷贝到项目的指定目录比如 `/files`。


修改一下项目的 Dockerfile 文件， 把项目中的SkyWalking Agent压缩包解压并拷贝到容器中的指定目录，比如`/usr/local`：


```
# 把项目中的SkyWalking Agent解压拷贝至容器中的指定目录中
COPY files/apache-skywalking-java-agent-9.3.0.tar /usr/local
RUN tar -xvf /usr/local/apache-skywalking-java-agent-9.3.0.tar -C /usr/local
```


启动参数里面加Java Agent的设置：


```
-javaagent:skywalking-agent/skywalking-agent.jar -Dskywalking.agent.service_name=planner-workbench-server -Dskywalking.collector.backend_service=192.168.1.86:11800
```


其中：

* javaagent 配置skywalking-agent.jar文件在解压包中的位置
* skywalking.agent.service_name 配置你应用的名称。不同环境可用加环境后缀进行区分。例如：planner-workbench-server-dev、planner-workbench-server-sit
* skywalking.collector.backend_service 配置SkyWalking平台地址。其中11800为默认接入端口


上述配置，也可用通过容器的环境变量来修改

* SKYWALKING_AGENT_SERVICE_NAME=planner-workbench-server
* SKYWALKING_COLLECTOR_BACKEND_SERVICE=192.168.1.86:11800


参考：<https://skywalking.apache.org/docs/skywalking-java/latest/en/setup/service-agent/java-agent/readme/#setup-java-agent>

### Node.js项目接入



安装SkyWalking NodeJS包：

```
$ npm install --save skywalking-backend-js
```


设置NodeJS Agent


```
import agent from 'skywalking-backend-js';

agent.start();
```


配置应用的名称和SkyWalking平台地址:

```
agent.start({
  serviceName: 'integration-ui',
  collectorAddress: '192.168.1.86:11800',
});
```


