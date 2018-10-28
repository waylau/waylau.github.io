---
layout: post
title: JVM（Java SE 11版本）加载类和接口
date: 2018-10-28 00:22
author: admin
comments: true
categories: [JVM]
tags: [JVM,Java]
---


本文介绍了Java虚拟机（Java SE 11版本）加载类和接口。

<!-- more -->


## 加载类和接口

加载是指查找具有特定名称的类或接口类型的二进制形式的过程。典型的做法是，查找事先由Java编译器从源代码计算而来二进制表示，但也可能是通过动态计算。
二进制形式最终会构造成一个Class对象。

加载的精确语义在Java Java Machine Specification，Java SE 11 Edition的第5章中给出。在这里，我们从Java编程语言的角度概述了该过程。

类或接口的二进制格式通常是上面引用的Java虚拟机规范Java SE 11版中描述的类文件格式，但只要满足第13.1节中规定的要求，其他格式也是可能的。


类ClassLoader的方法defineClass可用于从类文件格式的二进制表示构造Class对象。

表现良好的类加载器维护这些性质：

* 给定相同的名称，一个好的类加载器应该总是返回相同的类对象。
* 如果类加载器L1将类C的加载委托给另一个加载器L2，那么对于作为直接超类或C的直接超接口出现的任何类型T，或作为C中的字段类型，或作为类型方法的正式参数或
C中的构造函数，或者作为C，L1和L2中方法的返回类型应该返回相同的Class对象。

恶意类加载器可能违反这些性质。但是，它不能破坏类型系统的安全性，因为Java虚拟机可以防范这种情况。

*有关这些问题的进一步讨论，请参阅Java虚拟机规范，Java SE 11版和Java虚拟机中的动态类加载，作者：Sheng Liang和Gilad Bracha，作为ACO SIGPLAN发布的OOPSLA '98会议录。通告，第33卷，第10期，1998年10月，第36-44页。Java编程语言设计的基本原则是运行时类型系统不能被用Java编程语言编写的代码破坏，即使是这样的实现也是如此。否则敏感的系统类如ClassLoader和SecurityManager。*

## 加载过程

加载过程由类ClassLoader及其子类实现。

ClassLoader的不同子类可以实现不同的加载策略。特别地，类加载器可以缓存类和接口的二进制表示，基于预期的使用来预取它们，或者将一组相关的类加载在一起。
例如，如果找不到新编译的类，因为旧版本由类加载器缓存，这些活动可能对正在运行的应用程序不完全透明。但是，类加载器的责任是仅在程序中可能出现的情况下反映加载错误，而无需预取或组加载。

如果在类加载期间发生错误，那么将在程序中（直接或间接）使用该类型的任何点抛出类LinkichError的以下子类之一的实例：

* ClassCircularityError：无法加载类或接口，因为它将是自己的超类或超接口（第8.1.4节，第9.1.3节，第13.4.4节）。
* ClassFormatError：声称指定所请求的编译类或接口的二进制数据格式错误。
* NoClassDefFoundError：相关类加载器无法找到所请求的类或接口的定义。

因为加载涉及新数据结构的分配，所以它可能会因OutOfMemoryError而失败。


## 参考引用

* Java 虚拟机规范（第11版）》：<https://github.com/waylau/java-virtual-machine-specification>