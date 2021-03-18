---
layout: post
title: JDK 16发布
date: 2020-09-15 00:22
author: admin
comments: true
categories: [JDK,Java]
tags: [JDK,Java]
---

JDK 16已经于2021年3月16日如期发布。本文介绍JDK 16新特性。

<!-- more -->

## 发布版本说明

根据发布的规划，这次发布的 JDK 16 将是一个短期的过度版。下一个长期支持版（LTS 版）会在今年的 9 月份候发布（Java 17），LTS 版每 3 年发布一个，上一次长期支持版是 18 年 9 月发布的 JDK 11。 

JDK 16是Java SE平台版本16的开源参考实现，由[JSR 390](https://openjdk.java.net/projects/jdk/16/spec/)在JCP（Java Community Process）指定。

## 安装包下载

主要分为OpenJDK版本和Oracle版本，下载地址如下：

* OpenJDK版本：<https://jdk.java.net/16/>
* Oracle版本：<https://www.oracle.com/java/technologies/javase-jdk16-downloads.html>


上述版本，如果是个人学习用途，则差异不大。但如果是用于商业用途，则需要仔细看好相关的授权。Oracle JDK根据二进制代码许可协议获得许可，而OpenJDK根据GPL v2许可获得许可。 


更多有关Java的基本知识，可以参阅《[Java核心编程](https://item.jd.com/12868796.html)》这本书，描述的非常详细。


## JDK 16 新特性说明

JDK 16 为用户提供了17项主要的增强/更改，包括全新的 Java 语言改进，工具和内存管理，以及还有一些孵化和预览特性，有了这些新功能，Java 会进一步提高开发人员的生产力。

值得关注的变化是，JDK 14 中提供的预览特性：模式匹配和记录（Records），经过一年的社区反馈和实际应用，终于在 JDK 16 中完成最终落地了。

另外，Oracle 还为 Java SE 订阅服务中免费提供 GraalVM 企业版服务，GraalVM 可以帮助提高应用程序的性能并减少资源消耗，尤其是在微服务和云原生架构中。

### 1. 338: Vector API （孵化）

这个不是集合中的Vector，而是一个新的初始迭代孵化器模块`jdk.incubator.vector`，用于表示在运行时可靠地编译到支持的 CPU 架构上的最佳矢量硬件指令的矢量计算。

### 2. 347: Enable C++14 Language Features


允许在 JDK 底层的C++源代码中使用C++14的新语言特性，并且提供了在HotSpot虚拟机代码中，哪些代码使用了这些新特性的指南。


### 3. 357: Migrate from Mercurial to Git

将 OpenJDK 社区的源代码存储库从 Mercurial 迁移到 Git。

### 4. 369: Migrate to GitHub

在 GitHub 上托管 OpenJDK 社区的 Git 存储库。

[GitHub](https://github.com/waylau/github-help) 是世界流行的Git代码托管平台。在国内，托管代码推荐[Gitee](https://gitee.com/)哦。

### 5. 376: ZGC: Concurrent Thread-Stack Processing


ZGC 最早是在 JDK 11 中集成进来的，在 JDK 15 中正式转正。

这个版本则是为了让 ZGC 支持并发栈处理，解决了最后一个重大瓶颈，把 ZGC 中的线程栈处理从安全点移到了并发阶段。并且还提供了一种机制，使得其他 HotSpot 子系统可以通过该机制延迟处理线程栈。


### 6. 380: Unix-Domain Socket Channels

UNIX 域套接字通道，为 java.nio.channels 包中的套接字通道和服务端套接字通道 APIs 增加 Unix 域套接字通道所有特性支持。

UNIX 域套接字主要用于同一主机上的进程间通信（IPC），大部分方面与 TCP/IP套接字类似，不同的是 UNIX 域套接字是通过文件系统路径名寻址，而不是通过 IP 地址和端口号。

### 7. 386: Alpine Linux Port

在 x64 和 AArch64 平台体系结构上，将 JDK 移植到 Alpine Linux 以及使用 musl 作为其主要 C 语言库的其他 Linux 发行版中。

### 8. 387: Elastic Metaspace

弹性的元空间，可以帮助 HotSpot 虚拟机，将元空间中未使用的 class 元数据内存更及时地返回给操作系统，以减少元空间的内存占用空间。

另外，还简化了元空间的代码，以降低维护成本。



### 9. 388: Windows/AArch64 Port

将 JDK 移植到 Windows/ AArch64 平台系列。

### 10. 389: Foreign Linker API （孵化）

引入了一个新的 API，该 API 提供了对本地 native 代码的静态类型访问支持。


### 11. 390: Warnings for Value-Based Classes

基于值的类的警告，将基础类型包装类指定为基于值的类，废除其构造函数以进行删除，从而提示新的弃用警告。并且提供了在任何基于值的类的实例上不正常进行同步的警告。


### 12. 392: Packaging Tool

提供了 jpackage 打包工具，可用于打包独立的 Java 应用程序。

jpackage 打包工具是在 JDK 14 中首次作为孵化工具引入的新特性，到了 JDK 15 它仍然还在孵化中，现在它终于转正了。


### 13. 393: Foreign-Memory Access API (三次孵化)


该 API 允许 Java 应用程序安全有效地访问 Java 堆之外的外部内存。

这个最早在 JDK 14 中成为孵化特性，JDK 15/ JDK 16 中继续二、三次孵化并对其 API 有了一些更新，这个可以在 JDK 17 中好好期待一下转正。


### 14. 394: Pattern Matching for instanceof

模式匹配 for instanceof，相当于是增强的 instanceof，在 JDK 14 中首次成为预览特性，在 JDK 16 中正式转正。

模式匹配的到来将使得 instanceof 变得更简洁、更安全，为什么这么说，请看下面的示例。

Java 14 之前用法：


```java
if (obj instanceof String) {
    String s = (String) obj;
    // 使用s
}
```


Java 14之后的用法：

```java
if (obj instanceof String s) {
    // 使用s
}
```


### 15. 395: Records

简单来说，Records 就是一种新的语法糖，目的还是为了简化代码，在 JDK 14 中首次成为预览特性，在 JDK 16 中正式转正。

Records 可以在一定程度上避免低级冗余的代码，比如：constructors, getters, equals(), hashCode(), toString() 方法等，相当于 Lombok 的 @Data 注解，但又不能完全替代。

下面来看一个示例：

旧写法：

```java
class Point {
    private final int x;
    private final int y;
 
    Point(int x, int y) { 
        this.x = x;
        this.y = y;
    }
 
    int x() { return x; }
    int y() { return y; }
 
    public boolean equals(Object o) { 
        if (!(o instanceof Point)) return false;
        Point other = (Point) o;
        return other.x == x && other.y = y;
    }
 
    public int hashCode() {
        return Objects.hash(x, y);
    }
 
    public String toString() { 
        return String.format("Point[x=%d, y=%d]", x, y);
    }
}
```


新的Records写法：

```java
record Point(int x, int y) { }
```

### 16. 396: Strongly Encapsulate JDK Internals by Default


JDK 内部默认强封装，JDK 16 开始对 JDK 内部大部分元素默认进行强封装，sun.misc.Unsafe 之类的关键内部 API 除外，从而限制对它们的访问。

此外，用户仍然可以选择自 JDK 9 以来的默认的宽松的强封装，这样可以帮助用户毫不费力地升级到未来的 Java 版本。


### 17. 397: Sealed Classes (二次预览)


封闭类（二次预览），可以是封闭类和或者封闭接口，用来增强 Java 编程语言，防止其他类或接口扩展或实现它们。


## 参考引用

* 本文同步至: <https://waylau.com/jdk-16-released/>
<https://waylau.com/jdk-15-released/>
<https://waylau.com/jdk-14-released/>
* 《[Java核心编程](https://item.jd.com/12868796.html)》
* <https://github.com/waylau/modern-java-demos>