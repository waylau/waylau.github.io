---
layout: post
title: JDK 19的7 个新特性
date: 2022-08-02 00:22
author: admin
comments: true
categories: [JDK]
tags: [JDK]
---

JDK 19 功能集目前已经冻结，按照预期，在今年的9月份Java 19将会正式发布。带大家一起看下 JDK 19的这7 个冻结的新特性。

<!-- more -->
 


## 记录模式 （Record Patterns，预览版）

使用 记录模式 增强 Java 编程语言以解构记录值，可以嵌套记录模式和类型模式，实现强大的、声明性的和可组合的数据导航和处理形式。

这是一个预览语言功能。

## Linux/RISC-V 移植

将 JDK 移植到 Linux/RISC-V，目前仅支持 RISC-V 的 RV64GV 配置（包含向量指令的通用 64 位 ISA）。将来可能会考虑支持其他 RISC-V 配置，例如通用 32 位配置 (RV32G)。

## 外部函数和内存 API （Foreign Function & Memory API，预览版）

引入一个 API，Java 程序可以通过该 API 与 Java 运行时之外的代码和数据进行互操作。通过该 API 可有效地调用外部函数（ JVM 之外的代码）和安全地访问外部内存（不受 JVM 管理的内存），使得 Java 程序能够调用本机库并处理本机数据，而不会出现 JNI 的脆弱性和危险。

这是个预览版 API 。

## 虚拟线程（Virtual Threads，预览版）

将虚拟线程引入 Java 平台。虚拟线程是轻量级线程，可显著地减少编写、维护和观察高吞吐量并发应用程序的工作量。

有关虚拟线程的内容，在之前的博客中也有介绍。这里就不再赘述。

## Vector API （第四次孵化）

引入一个 API 来表达在运行时能够可靠编译的向量计算，在支持的 CPU 架构上优化向量指令，从而实现优于标量计算的性能。

## Switch 模式匹配（第三预览版）

用 switch 表达式和语句的模式匹配，以及对模式语言的扩展来增强 Java 编程语言。将模式匹配扩展到 switch 中，允许针对一些模式测试表达式，这样就可以简明而安全地表达复杂的面向数据的查询。

该特性最早在 Java 17 中作为预览版出现， Java 19 为第三次预览。

## 结构化并发（Structured Concurrency ，孵化阶段）

引入用于结构化并发的 API 来简化多线程编程，结构化并发将不同线程中运行的多个任务视为单个工作单元，从而简化错误处理、提高可靠性并增强可观察性。

这是一个孵化阶段的 API。

## 参考引用

* <https://openjdk.org/projects/jdk/19/>
* <https://waylau.com/jep-425-virtual-threads-preview/>