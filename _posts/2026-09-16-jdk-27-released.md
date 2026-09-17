---
layout: post
title: JDK 27发布
date: 2026-09-16 00:22
author: admin
comments: true
categories: [JDK,Java]
tags: [JDK,Java]
---

JDK 27 是 Oracle 发布的非LTS版本，2026年9月15日正式GA，JDK 27带来数千项性能、稳定性、安全性与开发生产力方面的改进，为Java持续创新筑牢根基。为帮助企业在后量子时代实现更安全的通信，Java 27实现TLS 1.3混合密钥交换，升级后量子密码学（PQC）能力。甲骨文同时放出**JDK 28早期访问版（EA）**，供用户预览Valhalla项目等未来技术。

除此之外，甲骨文还同步更新Java生态与**Oracle Java认证产品组合（JVP）**：面向轻量级微服务的Helidon 27、用于富客户端应用的JavaFX 27；Oracle Jipher 20现已纳入JVP，帮助客户在遵循FIPS 140规范的环境部署Java应用。

本文总结了JDK 27发布的新特性。



<!-- more -->



## 安装包下载

主要分为OpenJDK版本和Oracle版本，下载地址如下：

* OpenJDK版本：<https://jdk.java.net/27/>
* Oracle版本：<https://www.oracle.com/java/technologies/downloads/>


上述版本，如果是个人学习用途，则差异不大。但如果是用于商业用途，则需要仔细看好相关的授权。Oracle JDK商用需要遵循 [Oracle No-Fee Terms and Conditions (NFTC)](https://www.oracle.com/downloads/licenses/no-fee-license.html)协议，而OpenJDK根据[GPL v2许可](https://openjdk.java.net/legal/gplv2+ce.html)获得许可。 


更多有关Java的基本知识，可以参阅《[Java核心编程](https://item.jd.com/12868796.html)》这本书，描述的非常详细。


## JDK 27 新特性说明


### 后量子安全、合规与可信特性

**JEP 527：TLS 1.3后量子混合密钥交换**
帮助企业抵御“先收集、后解密”攻击，强化敏感数据与核心业务通信防护；开发者只需极少改动应用，即可满足不断演进的密码学规范。对于企业AI系统、关键业务应用尤为重要，这类系统需要在各类数据服务、模型、智能代理、API、云平台、分布式应用环境之间安全通信。

**JEP 538：密码对象PEM编码（第三次预览版）**
优化常用密码材料的安全处理，提升与主流安全工具、基础设施的互操作性。让Java应用可以更便捷对接现代身份认证、加密、证书管理系统，支撑企业AI安全落地。

**JEP 536：JFR进程内数据脱敏**
在保障JFR诊断能力不变的前提下，降低敏感信息泄露风险，让生产环境故障排查更加安全，满足合规、隐私与安全要求。对处理客户、员工、业务及受监管敏感数据的AI系统意义重大。

### AI、数据与现代化改造相关特性

**JEP 537：向量API（第12次孵化）**
无需编写专用原生代码即可提升吞吐，加速数据分析、AI推理、科学计算与媒体类业务。向量API利用硬件加速数据处理，在保留Java可移植、易维护、高安全特性的同时提升应用性能，用更少硬件创造更多业务价值。

**JEP 533：结构化并发（第七次预览版）**
面向云、微服务以及AI应用，降低开发风险，帮助开发者构建高可用、具备弹性的服务，减少并发缺陷。适用于包含大量并行任务的AI工作负载，例如模型调用、数据拉取、数据增强、排序、流程编排、实时决策。同时优化多线程代码的可靠性、任务取消、异常处理与可观测性，加快项目开发进度。

**JEP 532：模式、instanceof与switch中支持基本类型（第五次预览版）**
统一Java语法、提升表达能力，帮助开发团队更快将AI推理逻辑融入业务代码，减少编码错误。提升处理海量数据、面向决策场景应用（AI、数据分析、事件处理）的开发效率与代码可读性，这类应用经常混合使用基本数据类型与对象。

**JEP 531：延迟常量（第三次预览版）**
对常量初始化时机提供更强可控能力，同时保留常量带来的性能与正确性优势，优化资源利用率。对于对响应速度、扩展性、资源开销敏感的AI应用，可以改善使用体验，降低运行成本。

### 性能与运行时效率更新

**JEP 534：默认启用压缩对象头**
默认开启压缩对象头，降低JVM内存开销，加速应用现代化改造。企业直接将现有应用迁移至新版JDK，就能减少内存占用。对于大规模Java业务集群，能够提升应用部署密度、降低基础设施成本，增强云服务、微服务、数据密集型与AI业务的扩展能力。

**JEP 523：所有环境将G1设为默认垃圾收集器**
统一各部署环境的性能表现与运行行为，简化应用迁移和现代化工作。让开发团队部署云、微服务、AI类Java应用时更加省心，减少不同环境之间的配置差异。

### 其他安全、可靠性与性能增强

除上述9项重点JEP提案外，Java 27还包含大量非JEP改进：进一步完善后量子密码学、提升密码运算性能、增强运行时可观测性、优化诊断能力。

Java 27的各项特性，是甲骨文联合全球Java开发者社区，依托OpenJDK与Java社区进程（JCP）持续协作的成果。想要了解Java 27完整特性，可阅读Java 27技术博客。

## Valhalla项目与Leyden项目：面向未来的技术创新

甲骨文在JDK 28早期访问版中放出Valhalla项目首个重要发布阶段预览，平台创新能力距离正式并入JDK主线更进一步。Valhalla项目旨在弥合长期存在的Java对象与基本类型之间的鸿沟，以值类为起点，赋能数据分析、金融、科学计算、AI等数据密集型业务。该项目分阶段落地，目标是在不改变Java编程模型的前提下，让Java的数据存储更加紧凑高效。企业与开发者可以借此提前了解未来面向大数据应用（分析、金融、科学计算、AI系统）的优化方向。

甲骨文也在持续推进Leyden项目，目标改善程序启动速度、到达峰值性能的耗时以及内存占用，包含提前编译（AOT）等技术。Valhalla与Leyden两大项目，展现Java如何通过精心设计的平台能力持续迭代演进。

## Helidon 27：开发高速轻量微服务

Helidon 27是开源云原生Java微服务框架最新版本，基于Java虚拟线程打造高性能Web内核。Helidon现已对齐OpenJDK的版本号与发布节奏，纳入Java认证产品组合，方便客户规划升级，获得更清晰的兼容性与技术支持保障。

Helidon 27主要特性：
- **基于虚拟线程构建**：开发者编写简洁同步代码，就可以获得云原生服务需要的高并发能力。结合作用域值（Scoped Values），依托Java虚拟线程，编写高并发应用，代码更简洁、扩展性更强、资源利用率更高。
- **声明式API**：提供gRPC、消息队列等全新模块，基于注解的开发模式简单易用；框架本身轻量化，第三方依赖少，运行高效。
- **Helidon消息组件**：构建消息驱动应用，框架本身轻量化，第三方依赖少；支持可插拔连接器对接Kafka、JMS等各类消息中间件，业务代码无需绑定特定消息系统。
- **Helidon Data JDBC**：轻量化关系型数据库访问方案，开发者自主掌控SQL，降低运行时复杂度。

## JavaFX 27：打造丰富交互式用户界面

JavaFX 27是Java平台现代化客户端框架，用于构建交互体验丰富的桌面应用，可实现AI分析仪表盘、运维控制台、数据可视化工具等。纳入Java认证产品组合，可获得甲骨文商业技术支持，让开发与维护Java UI应用更有保障。

JavaFX 27在性能、易用性、无障碍访问上持续优化：支持macOS金属渲染管线、增强富文本编辑控件，持续完善无障碍相关功能。

## Oracle Jipher 20：满足FIPS 140规范，正式加入JVP

Oracle Jipher 20封装经过FIPS 140‑3认证的OpenSSL密码模块，现已纳入Java认证产品组合，可获得甲骨文官方支持、治理以及明确的产品路线。Jipher 20支持新版JDK，支持ML‑KEM、ML‑DSA后量子算法，通过标准Java API提供KDF、HKDF能力；自JDK25起支持基于外部函数内存API（FFM）对接OpenSSL。更多详情可阅读Oracle Jipher 20博客。




## 参考引用

* 本文同步至:  <https://waylau.com/jdk-27-released/>
* <https://waylau.com/jdk-26-released/>
* <https://waylau.com/jdk-25-released/>
* <https://waylau.com/jdk-24-released/>
* <https://waylau.com/jdk-23-released/>
* <https://waylau.com/jdk-22-released/>
* <https://waylau.com/jdk-21-released/>
* <https://waylau.com/jdk-17-released/>
* <https://waylau.com/jdk-16-released/>
* <https://waylau.com/jdk-15-released/>
* <https://waylau.com/jdk-14-released/>
* 《[Java核心编程](https://item.jd.com/12868796.html)》
* 开源项目“现代Java案例大全” <https://github.com/waylau/modern-java-demos>