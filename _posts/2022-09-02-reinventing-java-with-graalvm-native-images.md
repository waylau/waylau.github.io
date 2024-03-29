---
layout: post
title: 使用GraalVM原生映像彻底革新Java
date: 2022-09-02 00:22
author: admin
comments: true
categories: [GraalVM]
tags: [GraalVM,Java]
---

关键要点

* GraalVM Native Image是一种提前编译技术，可生成本机平台可执行文件。
* 本机可执行文件是容器和云部署的理想选择，因为它们体积小，启动速度非常快，而且需要的CPU和内存明显减少。
* 在无分布甚至Scratch容器映像上部署本机可执行文件，以减小大小并提高安全性。
* 通过配置文件引导的优化和G1垃圾收集器，使用GraalVM Native Image构建的本机可执行文件可以实现与JVM相当的峰值吞吐量。
* GraalVM Native Image在Spring Boot、Micronaut、Quarkus、Gluon Substrate等领先Java框架的支持下获得了大量采用。
* Java主导了企业应用。但在云中，Java比一些竞争对手更昂贵。本机编译使云中的Java更便宜：它创建的应用程序启动速度更快，内存使用更少。

<!-- more -->
 



GraalVM自三年前推出以来，在Java开发中引发了一场革命。GraalVM讨论最多的功能之一是Native Image，它基于提前（AOT）编译。它解锁了本机应用程序的运行时性能配置文件，同时保持了Java生态系统中熟悉的开发人员生产力和工具。

## Java应用程序的传统执行

Java平台中最强大和最有趣的部分之一，实现了卓越的峰值性能，是Java虚拟机（JVM）执行代码的方式。

首次运行应用程序时，虚拟机将解释代码并收集分析信息。尽管JVM解释器的性能很好，但它没有运行编译代码那么快。这就是为什么Oracle的JVM (HotSpot)还包含实时（JIT）编译器，这些编译器在程序执行时将应用程序代码编译为计算机代码。因此，如果您的代码“预热”-频繁执行，它将被C1 JIT编译器编译为机器码。然后，如果它仍然执行得足够频繁，并达到某些阈值，它将由顶级JIT编译器（C2或Graal编译器）编译。顶层编译器根据有关哪些代码分支最常执行、循环执行频率以及多态代码中使用哪些类型的分析信息执行优化。

有时编译器会执行推测性优化。例如，JVM可以根据它收集的分析信息生成方法的优化、编译版本。但是，由于JVM上的代码执行是动态的--如果它所做的假设在以后失效--JVM将去优化：它将忽略编译的代码并恢复到解释模式。正是这种灵活性使JVM如此强大：它开始快速执行代码，利用优化编译器来处理频繁执行的代码，并推测应用更积极的优化。

乍一看，这种方法似乎是运行应用程序的理想方式。然而，像大多数事情一样，即使是这种方法也会带来成本和权衡；那么它们在这里是什么呢？当JVM执行其操作（如验证代码、加载类、动态编译和收集分析信息）时，它将进行需要大量CPU时间的复杂计算。除了这个成本之外，JVM还需要大量的内存来存储分析信息，并需要相当大的时间和内存来启动。随着许多公司将应用程序部署到云中，这些成本变得更加重要，因为启动时间和内存直接影响部署应用程序的成本。那么，有没有办法减少启动时间和内存使用，并保持我们都喜欢的Java生产力、库和工具？

答案是“是”，这就是GraalVM Native Image所做的。

## GraalVM for Win

GraalVM 10年前开始是Oracle Labs的一个研究项目。Oracle实验室是Oracle的一个研发分支，研究编程语言和虚拟机、机器学习和安全、图形处理和其他领域。GraalVM是Oracle Labs的一个很好的例子--它基于多年的研究和100多篇发表的学术论文。

项目的核心是Graal编译器--一个现代的、高度优化的编译器，从零开始创建。多亏了许多高级优化，在许多情况下，它生成的代码比C2编译器更好。其中一个优化是部分逃逸分析：它通过在对象不逃逸编译单元的分支中的标量替换来删除堆上不必要的对象分配，并且Graal编译器确保对象存在于堆中的分支中。

这种方法减少了应用程序的内存占用，因为堆上的对象较少。它还降低了CPU负载，因为需要减少垃圾收集。此外，GraalVM中的高级猜测通过利用动态运行时反馈生成更快的机器代码。通过推测某些程序部分在执行期间不会运行，GraalVM编译器可以使代码更加高效。

您可能会惊讶地发现Graal编译器主要是用Java编写的。如果您查看GraalVM的核心GitHub存储库，您会发现90%以上的代码是用Java编程语言编写的，这再次证明了Java是多么强大和通用。

## 本机映像的工作原理

Graal编译器还作为提前（AOT）编译器工作，生成本机可执行文件。考虑到Java的动态性质，这到底是如何工作的？

与JIT模式不同，编译和执行同时发生，在AOT模式下，编译器在构建期间执行所有编译，在执行之前。这里的主要想法是将所有的“繁重”--昂贵的计算--转移到构建时间上，这样它就可以完成一次，然后在运行时生成的可执行文件快速启动，从一开始就准备好了，因为所有的东西都是预先计算和预编译的。

GraalVM“本机映像”实用程序将Java字节码作为输入，并输出本机可执行文件。为此，实用程序在封闭世界假设下对字节码执行静态分析。在分析期间，实用程序查找应用程序实际使用的所有代码，并消除所有不必要的代码。

这三个关键概念有助于您更好地理解本机映像生成过程：

* 点到分析。GraalVM本机映像确定哪些Java类、方法和字段在运行时可访问，并且只有这些类、方法和字段将包含在本机可执行文件中。点到分析从所有入口点开始，通常是应用程序的主要方法。分析迭代处理所有可传递的代码路径，直到到达固定点并结束分析。这不仅适用于应用程序代码，还适用于库和JDK类--将应用程序打包为自包含二进制文件所需的一切。
* 构建时初始化。GraalVM Native Image默认在运行时初始化类，以确保行为正确。但是，如果Native Image可以证明某些类可以安全初始化，它将在构建时初始化它们。这使得运行时初始化和检查变得不必要，并提高了性能。
* 堆快照。Native Image中的堆快照是一个非常有趣的概念，值得有自己的文章。在映像构建过程中，静态初始化器分配的Java对象以及所有可访问的对象都会写入映像堆。这意味着应用程序使用预填充堆启动速度要快得多。

有趣的是，点到分析使对象在图像堆中可访问，而构建图像堆的快照可以使新方法可访问点到分析。因此，点到分析和堆快照将迭代执行，直到达到固定点：

![](https://communityfile-drcn.op.hicloud.com/FileServer/getFile/cmtybbs/042/413/002/0000000000042413002.20220902092735.95081690893211946281447375832481:50531002070057:2800:B9FA13E6E1191F6C0D49276BCB051A82E6C1076D8E203A09D729B040CCFEB476.jpeg)

## 原生镜像构建流程

分析完成后，Graal将所有可访问的代码编译为特定于平台的本机可执行文件。该可执行文件本身功能齐全，不需要JVM运行。因此，您可以获得一个纤薄而快速的Java应用程序本机可执行版本：一个执行完全相同的功能，但只包含必要的代码及其所需的依赖项。

但是，谁来负责本机可执行文件中的内存管理和线程调度等功能呢？为此，本机映像包括Substrate VM -一个提供运行时组件的精简VM实现，如垃圾收集器和线程调度器。就像Graal编译器一样，Substrate VM是用Java编程语言编写的，并由GraalVM Native Image编译成本机代码！

多亏了AOT编译和堆快照，本机映像为您的Java应用程序提供了全新的性能配置文件。接下来让我们仔细看看这个。

## 将Java启动性能提升到一个新的水平

您可能听说过，由Native Image生成的可执行文件具有很好的启动性能。但这到底意味着什么？

即时启动。与在JVM上运行不同的是，在JVM上，代码首先被验证、解释，然后（预热后）最终编译，本机可执行文件从一开始就附带了优化的机器码。我喜欢使用的另一个术语是即时性能--应用程序可以在执行的最初毫秒内执行有意义的工作，而没有任何分析或编译开销。

### JIT和原生镜像模式的启动时间效应



内存效率。本机可执行文件既不需要JVM及其JIT编译基础架构，也不需要用于编译代码、配置文件数据和字节码缓存的内存。它所需要的只是可执行文件和应用程序数据的内存。以下是一个例子：

![](https://communityfile-drcn.op.hicloud.com/FileServer/getFile/cmtybbs/042/413/002/0000000000042413002.20220902092735.61291530463246524807238088453828:50531002070057:2800:1406E7EB45E1DF1435833A45ACC862271AFF6D347FC3FC69AEB0889461B32B62.jpeg)

### JIT和Native镜像模式下的内存和CPU占用率

上面的图表显示了Web服务器在JVM上（左）和作为本机可执行文件（右）的运行时行为。青绿色线显示了使用的内存：JIT模式下200 MB，而本机可执行文件为40 MB。红线显示CPU活动：JVM在前面描述的预热JIT活动期间大量使用CPU，而本机可执行文件几乎不使用CPU，因为所有昂贵的编译操作都发生在构建时。这种快速且资源高效的运行时行为使Native Image成为一个很好的部署模型，在这种模型中，以更短的时间使用更少的资源显著降低成本-微服务、无服务器和一般云工作负载。

包装尺寸。本机可执行文件仅包含所需的代码。这就是为什么它比应用程序代码、库和JVM的总和要小得多。在某些情况下，例如在资源受限的环境中工作，应用程序的打包大小可能很重要。公用事业，如UPX进一步压缩本机可执行文件。

## 与JVM不等的峰值性能

不过，峰值性能呢？当所有内容都提前编译时，本机映像如何优化运行时的峰值吞吐量？

我们正在努力确保本机映像提供卓越的峰值性能和快速启动。已经有几种方法可以提高本机可执行文件的峰值性能：

* 配置文件引导优化。由于Native Image会提前优化和编译代码，默认情况下，它无法访问运行时分析信息来在应用程序运行时优化代码。解决这一问题的一种方法是使用配置文件引导优化（PGO）。使用PGO，开发人员可以运行应用程序，收集分析信息，然后将其反馈到本机映像生成过程中。“本机映像”实用程序使用此信息根据应用程序的运行时行为优化生成的可执行文件的性能。PGO在GraalVM Enterprise中可用，这是GraalVM的商业版本，提供由甲骨文。
* 本机映像中的内存管理。由Native Image生成的可执行文件中的默认垃圾收集器是Serial GC，这对于具有小堆的微服务是最佳的。还有其他GC选项可用：
    * 串行GC现在有了一个新的策略，为年轻一代启用幸存者空间，以减少应用程序运行时内存占用。自引入此策略以来，我们测量了典型微服务工作负载（如Spring Petclinic）的峰值吞吐量提高高达23.22%。
    * 或者，您可以使用低延迟G1垃圾收集器以获得更好的吞吐量（在GraalVM Enterprise中可用）。G1最适合较大的堆。

通过PGO和G1 GC，本机可执行文件实现了与JVM相当的峰值性能：

![](https://communityfile-drcn.op.hicloud.com/FileServer/getFile/cmtybbs/042/413/002/0000000000042413002.20220902092735.31292074086699172040004400366975:50531002070057:2800:F80715F790FF76DA17D9B3B9BCE42D530DE4D0C27FB946949B393D42679B20D6.jpeg)

## 改变Java的云游戏

本机映像对云部署产生了巨大的影响，在云部署中，它可能会对应用程序的资源消耗配置文件产生巨大影响。我们已经了解到，由Native Image生成的本机可执行文件启动速度很快，需要的内存更少。它对云部署到底意味着什么，GraalVM如何帮助您最小化Java容器映像？

正如我们已经确定的那样，由原生映像生成的应用程序不需要JVM来运行：它们可以是自包含的，并包括应用程序执行所需的一切。这意味着您可以将应用程序放入一个纤薄的Docker映像中，并且它将独立运行。图像大小将取决于应用程序的功能以及它包括的依赖项。一个基本的“Hello,World!”应用程序，使用Java微服务框架构建，大约为20 MB。

使用本机映像，您还可以构建静态和大部分静态可执行文件。一个主要是静态的本机可执行文件静态链接到所有库，但容器映像提供的“libc”除外。您可以将所谓的无分布式容器映像用于轻量级部署。无分发映像仅包括运行应用程序的库，没有shell、包管理器和其他程序。

## 参考引用

* <https://www.infoq.com/articles/native-java-graalvm/>