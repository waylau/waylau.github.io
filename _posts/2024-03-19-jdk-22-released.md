---
layout: post
title: JDK 22发布
date: 2024-03-19 00:22
author: admin
comments: true
categories: [JDK,Java]
tags: [JDK,Java]
---

JDK 22已经于2024年3月19日正式发布。本文总结了JDK 22发布的新特性。

<!-- more -->



## 安装包下载

主要分为OpenJDK版本和Oracle版本，下载地址如下：

* OpenJDK版本：<https://jdk.java.net/22/>
* Oracle版本：<https://www.oracle.com/java/technologies/downloads/>


上述版本，如果是个人学习用途，则差异不大。但如果是用于商业用途，则需要仔细看好相关的授权。Oracle JDK商用需要遵循 [Oracle No-Fee Terms and Conditions (NFTC)](https://www.oracle.com/downloads/licenses/javase-license1.html)协议，而OpenJDK根据GPL v2许可获得许可。 


更多有关Java的基本知识，可以参阅《[Java核心编程](https://item.jd.com/12868796.html)》这本书，描述的非常详细。


## JDK 22 新特性说明


### JEP 423 Region Pinning for G1


通过在G1中实现区域钉扎来减少延迟，这样在Java Native Interface（JNI）关键区域期间就不需要禁用垃圾收集。

 
### JEP 447 Statements before super(...) （预览）

在Java编程语言的构造函数中，允许不引用正在创建的实例的语句出现在显式构造函数调用之前。这是一个预览语言功能。

### JEP 454 Foreign Function & Memory API

引入API，Java程序可以通过该API与Java运行时之外的代码和数据进行互操作。通过有效地调用外部函数（即JVM外部的代码），并通过安全地访问外部内存（即不受JVM管理的内存），API使Java程序能够调用本机库并处理本机数据，而不会出现JNI的脆弱性和危险性。


### JEP 456 Unnamed Variables & Patterns

使用未命名变量和未命名模式增强Java编程语言，当需要变量声明或嵌套模式但从未使用时，可以使用这些变量和模式。两者都用下划线字符_表示。

### JEP 457 Class-File API（预览版）

提供一个标准的API，用于解析、生成和转换Java类文件。这是一个预览API。

### JEP 458 Launch Multi-File Source-Code Programs


增强java应用程序启动器，使其能够运行作为java源代码的多个文件提供的程序。这将使从小程序到大程序的过渡更加渐进，使开发人员能够选择是否以及何时配置构建工具。

### JEP 459 String Templates（第二次预览）

使用字符串模板增强Java编程语言。字符串模板通过将文本与嵌入的表达式和模板处理器耦合来产生专门的结果，从而补充Java现有的字符串文本和文本块。这是一个预览语言功能和API。

### JEP 460 Vector API（第七培养箱）

引入API来表示向量计算，这些向量计算在运行时可靠地编译为支持的CPU架构上的最佳向量指令，从而实现优于等效标量计算的性能。

### JEP 461 Stream Gatherers（预览）

增强Stream API以支持自定义中间操作。这将允许流管道以现有内置中间操作无法轻松实现的方式转换数据。这是一个预览API。

### JEP 462 Structured Concurrency （第二次预览）

通过引入用于结构化并发的API来简化并发编程。结构化并发将在不同线程中运行的相关任务组视为单个工作单元，从而简化错误处理和消除，提高可靠性，并增强可观察性。这是一个预览API。

### JEP 463 Implicitly Declared Classes and Instance Main Methods（第二次预览）

发展Java编程语言，使学生无需理解为大型程序设计的语言功能即可编写第一个程序。学生们可以为单类程序编写精简的声明，而不是使用单独的语言方言，然后随着技能的发展，无缝地扩展程序，使用更高级的功能。这是一个预览语言功能。

### JEP 464 Scoped Values（第二次预览）

引入作用域值，使不可变数据能够与同一线程中的子帧和子线程进行托管共享。作用域值比线程本地变量更容易推理，并且空间和时间成本更低，尤其是与虚拟线程和结构化并发结合使用时。这是一个预览API。

## 参考引用

* 本文同步至:  <https://waylau.com/jdk-22-released/>
* <https://waylau.com/jdk-21-released/>
* <https://waylau.com/jdk-17-released/>
* <https://waylau.com/jdk-16-released/>
* <https://waylau.com/jdk-15-released/>
* <https://waylau.com/jdk-14-released/>
* 《[Java核心编程](https://item.jd.com/12868796.html)》
* 开源项目“现代Java案例大全” <https://github.com/waylau/modern-java-demos>