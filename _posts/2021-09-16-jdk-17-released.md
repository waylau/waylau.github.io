---
layout: post
title: JDK 17发布
date: 2021-09-16 00:22
author: admin
comments: true
categories: [JDK,Java]
tags: [JDK,Java]
---

JDK 17已经于2021年3月16日如期发布。本文介绍JDK 17新特性。JDK 17于2021年9月14日正式发布（General-Availability Release）。JDK 17将是大多数供应商的长期支持（LMS）版本。上一个LTS版本是JDK 11。

本文总结了JDK 17发布的新特性。

<!-- more -->

## 发布版本说明

根据发布的规划，这次发布的 JDK 17 将是一个长期支持版（LTS 版）。LTS 版每 3 年发布一个，上一次长期支持版是 18 年 9 月发布的 JDK 11。 

JDK 17是Java SE平台版本17的开源参考实现，由[JSR 392](https://openjdk.java.net/projects/jdk/17/spec/)在JCP（Java Community Process）指定。

## 安装包下载

主要分为OpenJDK版本和Oracle版本，下载地址如下：

* OpenJDK版本：<https://jdk.java.net/16/>
* Oracle版本：<https://www.oracle.com/java/technologies/downloads/>


上述版本，如果是个人学习用途，则差异不大。但如果是用于商业用途，则需要仔细看好相关的授权。Oracle JDK根据二进制代码许可协议获得许可，而OpenJDK根据GPL v2许可获得许可。 


更多有关Java的基本知识，可以参阅《[Java核心编程](https://item.jd.com/12868796.html)》这本书，描述的非常详细。


## JDK 17 新特性说明


### JEP 406：switch的模式匹配（预览）(JDK-8213076)

specification

通过switch表达式和语句的模式匹配，以及模式语言的扩展，增强Java编程语言。将模式匹配扩展到switch允许对表达式进行测试，每个模式都有特定的操作，以便可以简洁而安全地表达复杂的面向数据的查询。

有关更多详细信息，请参见[JEP 406](https://openjdk.java.net/jeps/406)

### JEP 409：密封类(JDK-8260514)

specification

密封类（Sealed Class）已添加到Java语言中。密封类和接口限制了哪些其他类或接口可以扩展或实现它们。

密封类由JEP 360并在JDK 15中作为预览功能交付。它们再次被提出，并进行了改进，由JEP 397并在JDK 16中作为预览功能提供。现在，在JDK 17中，密封类正在最终确定，与JDK 16没有任何更改。

有关更多详细信息，请参见[JEP 409](https://openjdk.java.net/jeps/409)

### JEP 382：新的macOS渲染管道(JDK-8238361)

client-libs/2d

Swing API用于渲染的Java 2D API现在可以使用新的Apple Metal加速渲染API 给macOS。

目前默认情况下，这是禁用的，因此渲染仍然使用OpenGL API，这些API被Apple弃用，但仍然可用和支持。

要启用金属，应用程序应通过设置系统属性指定其使用：

```
-Dsun.java2d.metal=true
```

Metal或OpenGL的使用对应用程序是透明的，因为这是内部实现的区别，对Java API没有影响。Metal管道需要macOS 10.14.x或更高版本。在早期版本上设置它的尝试将被忽略。

有关更多详细信息，请参见[JEP 382(https://openjdk.java.net/jeps/382)

### 大图标访问新API(JDK-8182043)

client-libs/javax.swing

JDK 17中提供了一个新的方法`javax.swing.filechooser.FileSystemView.getSystemIcon(File, int, int)`，该方法允许在可能的情况下访问更高质量的图标。它已为Windows平台完全实施；但是，其他平台上的结果可能会有所不同，稍后将增强。例如，通过使用以下代码：

```java
FileSystemView fsv = FileSystemView.getFileSystemView();

Icon icon = fsv.getSystemIcon(new File("application.exe"), 64, 64);

JLabel label = new JLabel(icon);
```

用户可以为“application.exe”文件获得更高质量的图标。此图标适用于创建可以在HighDPI环境中更好地扩展的标签。

### DatagramSocket可以直接加入多播组(JDK-8237352)

core-libs/java.net

java.net.DatagramSocket在此版本中已更新，以添加对加入多播组（multicast group）的支持。它现在定义了加入和离开多播组的加入组和离开组方法。java.net.DatagramSocket的类级API文档已更新，以解释如何配置普通DatagramSocket并用于加入和离开多播组。

此更改意味着DatagramSocket API可以用于组播应用程序，而无需使用旧的java.net.MulticastSocket API。MulticastSocket API的工作原理和以前一样，尽管它的大多数方法都被弃用了。

有关此变更理由的更多信息，请查看[CSRJDK-8260667](https://bugs.openjdk.java.net/browse/JDK-8260667)

### JEP 356：增强型伪随机数生成器(JDK-8193209)

core-libs/java.util

为伪随机数生成器（PRNG）提供新的接口类型和实现，包括可跳转的PRNG和一类额外的可拆分PRNG算法（LXM）。

有关更多详细信息，请参见[JEP 356](https://openjdk.java.net/jeps/356)

### Ideal Graph Visualizer的现代化(JDK-8254145)

hotspot/compiler

Ideal Graph Visualizer（IGV）是一个可视化和交互式地探索HotSpot VM C2即时（JIT）编译器中使用的中间表示的工具，已经现代化。增强功能包括：

* 支持在最多JDK 15上运行IGV（IGV底层NetBeans平台支持的最新版本）
* 更快的、基于Maven的IGV构建系统
* 块形成、组删除和节点跟踪的稳定
* 默认过滤器中更直观的着色和节点分类
* 具有更自然默认行为的排名快速节点搜索


现代化的IGV部分兼容从早期JDK版本生成的图形。它支持基本功能，如图形加载和可视化，但辅助功能，如节点聚类和着色可能会受到影响。

有关构建和运行IGV的详细信息，请参见<https://github.com/openjdk/jdk17/tree/master/src/utils/IdealGraphVisualizer>。

### “New API”的新页面和改进的“Deprecated”页(JDK-8263468)

tools/javadoc(tool)

JavaDoc现在可以生成一个页面，总结API中最近的更改。要包括的最近版本的列表是使用 `--since`命令行选项指定的。这些值用于查找具有匹配`@since`的声明，因为要包含在新页面上的标记。`--since-label`命令行选项提供了要在“New API”页面标题中使用的文本。

在总结已弃用项目的页面上，您可以查看按已弃用项目的版本分组的项目。

### 错误消息中的源详细信息(JDK-8267126)

tools/javadoc(tool)

当JavaDoc报告输入源文件中的问题时，它将以类似编译器（javac）诊断消息的方式显示问题的源行，以及包含指向该行位置的插入符号（^）的行。

此外，日志记录和其他“信息”消息现在写入标准错误流，留下标准输出流用于命令行选项特别请求的输出，如命令行帮助。

### JEP 412：外部函数和内存API（孵化）(JDK-8265033)

core-libs

引入一个API,Java程序可以通过该API与Java运行时之外的代码和数据互操作。通过有效地调用外部函数（即JVM外部的代码），并通过安全地访问外部内存（即不由JVM管理的内存），该API使Java程序能够调用本机库并处理本机数据，而不会有JNI的脆弱性和危险。

有关更多详细信息，请参阅[JEP 412](https://openjdk.java.net/jeps/412)

### 控制台字符集API(JDK-8264208)

core-libs

java.io.Console已更新，以定义一个新方法，该方法返回控制台的Charset。返回的Charset可能与Charset.defaultCharset()方法返回的Charset不同。例如，它返回IBM437，而Charset.defaultCharset()在Windows (en-US)上返回windows-1252。请参阅<https://bugs.openjdk.java.net/browse/JDK-8264209>了解更多详细信息。

### 用于反序列化的JDK Flight Recorder事件(JDK-8261160)

core-libs/java.io:serialization

现在可以使用JDK Flight Recorder (JFR)监控对象的反序列化。当启用JFR且JFR配置包括反序列化事件时，每当运行程序尝试反序列化对象时，JFR将发出事件。反序列化事件名为jfr.Derialization，默认情况下禁用。反序列化事件包含序列化筛选器机制使用的信息；请参阅对象输入筛选器规范。此外，如果启用了过滤器，JFR事件指示过滤器是接受还是拒绝对象的反序列化。有关如何使用JFR反序列化事件的更多信息，请参阅文章监控反序列化提高应用安全性。有关使用和配置JFR的参考信息，请参阅JFR运行时指南和JFR命令参考JDK任务控制文件的章节。

### JEP 415：实现特定于上下文的反序列化过滤器(JDK-8264859)

core-libs/java.io:serialization

JEP 415：特定于上下文的反序列化过滤器允许应用程序通过JVM范围的过滤器工厂配置特定于上下文的和动态选择的反序列化过滤器，该工厂被调用以为每个单独的反序列化操作选择过滤器。

用于序列化过滤的Java核心库开发人员指南介绍了用例，并提供了示例。

### 本机字符编码名称的系统属性(JDK-8265989)

core-libs/java.lang

引入了一个新的系统属性本机.encode。此系统属性提供基础主机环境的字符编码名称。例如，它通常在Linux和macOS平台中具有UTF-8，在Windows (en-US)中具有Cp1252。请参阅<https://bugs.openjdk.java.net/browse/JDK-8266075>了解更多详细信息。

### 添加java.time.InstantSource (JDK-8266846)

core-libs/java.time

引入了一个新的接口java.time.InstantSource。此接口是java.time.Clock的抽象，只关注当前时刻，不引用时区。

### 十六进制格式和解析实用程序(JDK-8251989)

core-libs/java.util

java.util.HexFormat为基元类型和字节数组提供十六进制和十六进制之间的转换。分隔符、前缀、后缀和大写或小写的选项由返回HexFormat实例的工厂方法提供。

### 实验Compiler Blackholes支持(JDK-8259316)

hotspot/compiler

增加了对Compiler Blackholes的实验支持。这些对于低级基准测试非常有用，以避免关键路径上的死代码消除，而不影响基准性能。当前的支持以CompileCommand的形式实现，可访问为-XX:CompileCommand=blackhole,<method>，并计划最终将其毕业到公共API。

JMH已经能够在指示/可用时自动检测和使用此设施。有关后续步骤，请查阅JMH文档。

### HotSpot JVM中的新类层次结构分析实现(JDK-8266074)

hotspot/compiler

HotSpot JVM中引入了一个新的类层次结构分析实现。它的特点是对抽象和默认方法的增强处理，从而改进了JIT编译器所做的内联决策。新实现将取代原始实现，并在默认情况下打开。

为了帮助诊断与新实现相关的可能问题，可以通过指定 `-XX:+UnlockDiagnosticVMOptions -XX:-UseVtableBasedCHA`命令行标志来打开原始实现。

原始实现可能会在未来的版本中删除。

### JEP 391: macOS/AArch64端口(JDK-8251280)

hotspot/compiler

macOS 11.0现在支持AArch64体系结构。此JEP在JDK中实现了对macos-aarch64平台的支持。添加的功能之一是支持W^X（write xor execute）内存。它仅对macos-aarch64启用，并可以在某些时候扩展到其他平台。JDK可以在英特尔计算机上交叉编译，也可以在基于Apple M1的计算机上编译。

有关更多详细信息，请参见[JEP 391](https://openjdk.java.net/jeps/391)

### 统一日志支持异步日志刷新(JDK-8229517)

hotspot/runtime

为了避免使用统一日志记录的线程中出现不希望的延迟，用户现在可以请求统一日志记录系统在异步模式下运行。这可以通过传递命令行选项-Xlog:async来完成。在异步日志记录模式下，日志站点将所有日志记录消息入队到缓冲区。独立线程负责将它们刷新到相应的输出。中间缓冲区是有界的。缓冲区耗尽时，入队消息将被丢弃。用户可以使用命令行选项-XX:AsyncLogBufferSize=<bytes>.来控制中间缓冲区的大小。

### ARM上的macOS早期访问可用(JDK-8266858)

infrastructure/build

新的macOS现在可用于ARM系统。ARM端口的行为应与英特尔端口类似。没有已知的功能差异。在macOS上报告问题时，请指定是使用ARM还是x64。

### 支持在Keytool -genkeypair命令中指定签名者(JDK-8260693)

security-libs/java.security

-signer和-signerkeypass选项已添加到keytool实用程序的-genkey对命令中。-signer选项指定签名者的私钥条目的密钥库别名，-signerkeypass选项指定用于保护签名者私钥的密码。这些选项允许keytool -genkey对使用签名者的私钥对证书进行签名。这对于生成具有密钥协商算法作为公钥算法的证书特别有用。

### SunJCE提供程序通过AES密码支持KW和KWP模式(JDK-8248268)

security-libs/javax.crypto

SunJCE提供程序已得到增强，以支持AES密钥换行算法（RFC 3394）和带填充算法的AES密钥换行算法（RFC 5649）。在早期版本中，SunJCE提供程序在“AESWrap”密码算法下支持RFC 3394，该算法只能用于包装和解包装密钥。通过此增强，增加了两种分组密码模式，KW和KWP，支持使用AES进行数据加密/解密和密钥包装/解包装。有关更多详细信息，请查看“JDK提供程序文档”指南的“SunJCE提供程序”部分。

### 新SunPKCS11配置属性(JDK-8240256)

security-libs/javax.crypto:pkcs11

SunPKCS11提供程序添加了新的提供程序配置属性，以更好地控制本机资源的使用。SunPKCS11提供程序使用本机资源以便与本机PKCS11库一起工作。为了管理和更好地控制本机资源，添加了额外的配置属性，以控制清除本机引用的频率，以及是否在注销后销毁基础PKCS11令牌。

SunPKCS11提供程序配置文件的3个新属性是：

* destroyTokenAfterLogout （布尔值，默认值为false）如果设置为true，则在SunPKCS11提供程序实例上调用java.security.AuthProvider.logout() 时，基础令牌对象将被销毁，资源将被释放。这基本上会在logout() 调用后使SunPKCS11提供程序实例不可用。请注意，不应将此属性设置为true的PKCS11提供程序添加到系统提供程序列表中，因为提供程序对象在logout() 方法调用后不可用。
* cleaner.shortInterval（整数，默认值为2000，以毫秒为单位）这定义了在繁忙期间清除本机引用的频率，即cleaner线程应多久处理队列中不再需要的本机引用以释放本机内存。请注意，cleaner线程将在200次失败尝试后切换到“longInterval”频率，即在队列中找不到引用时。
* cleaner.longInterval（整数，默认值为60000，以毫秒为单位）这定义了在非繁忙期间检查本机引用的频率，即cleaner线程应检查队列中的本机引用的频率。请注意，如果检测到用于清理的本机PKCS11引用，cleaner线程将切换回“短间隔”值。

### 具有系统属性的可配置扩展(JDK-8217633)

security-libs/javax.net.ssl

已添加两个新的系统属性。系统属性jdk.tls.client.disableExts用于禁用客户端中使用的TLS扩展。系统属性jdk.tls.server.disableExts用于禁用服务器中使用的TLS扩展。如果禁用了扩展，则在握手消息中既不会生成也不会处理扩展。

属性字符串是在IANA文档中注册的逗号分隔的标准TLS扩展名称列表（例如，server_name、status_request和签名_algorithms_cert）。请注意，扩展名区分大小写。未知、不支持、拼写错误和重复的TLS扩展名称令牌将被忽略。

请注意，阻止TLS扩展的影响是复杂的。例如，如果禁用了强制扩展，则可能无法建立TLS连接。请不要禁用强制扩展，除非您清楚地了解其影响，否则不要使用此功能。

### 包摘要页面上的“Related Packages”(JDK-8260388)

tools/javadoc(tool)

软件包的摘要页面现在包括一个列出任何“Related Packages”的部分。Related Packages（相关软件包）是根据常见命名约定启发式确定的，可能包括以下内容：

* “parent”包（即，包是子包的包）
* 同级包（即具有相同父包的其他包）
* 任何子包

相关软件包不一定都在同一个模块中。


## 参考引用

* 本文同步至: <https://waylau.com/jdk-17-released/>
<https://waylau.com/jdk-16-released/>
<https://waylau.com/jdk-15-released/>
<https://waylau.com/jdk-14-released/>
* 《[Java核心编程](https://item.jd.com/12868796.html)》
* 开源项目“现代Java案例大全” <https://github.com/waylau/modern-java-demos>