---
layout: post
title: 从Java 8升级到Java 11的注意事项
date: 2022-04-19 00:22
author: admin
comments: true
categories: [Java]
tags: [Java]
---


虽然Java最新版本已经发展到Java 18了，但市面上大部分的项目还在使用Java 8。由于从Java 8之后，Java API不一定向前兼容，因此很多人都对升级Java版本心存顾虑。Java 11是Java 8的下一个长期支持版本，毫无疑问Java 11比Java 8更加优秀。

本文介绍了将代码从 Java 8 转换到 Java 11用到的检查代码工具，还介绍了可能遇到的问题以及解决这些问题的建议。 

<!-- more -->



## 为什么需要Java 11


Java 11是Java 8的下一个长期支持版本，这意味着Java 8不再受到官方支持。其外，从 Java 8 到Java 11，Java平台也发生了很大的更改，这些更改都是让Java平台更加优秀。

本文重点介绍对性能、诊断和工作效率有影响的更改。

### 模块

模块解决在大型应用程序（在 classpath 上运行）中难以管理的配置和封装问题。 模块是 Java 类和接口以及相关资源的自述性集合。

有了模块，即可自定义那些仅包含应用程序所需组件的运行时配置。 此自定义产生的内存占用量较小，因此可以使用 jlink 将应用程序静态链接到用于部署的自定义运行时中。 这个较小的内存占用量可能特别适用于微服务体系结构。

在内部，JVM 可以通过让类加载更有效的方式利用模块。 结果就是，运行时更小、更轻便且启动速度更快。 JVM 用来改善应用程序性能的优化技术可以更有效，因为模块可以对某个类需要哪些组件进行编码。

对程序员来说，模块可以要求显式声明一个模块可以导出哪些包以及它需要哪些组件，并且可以限制反射访问，因此有助于强制实施强封装。 这种级别的封装使应用程序更安全，维护起来更容易。

应用程序可以继续使用 classpath，不需转换为作为必备组件的模块即可在 Java 11 上运行。

### Java 网络流量记录器

Java Flight Recorder (JFR) 从正在运行的 Java 应用程序中收集诊断和分析数据。 JFR 对正在运行的 Java 应用程序几乎没有影响。 收集的数据随后可以使用 Java Mission Control (JMC) 和其他工具进行分析。 虽然 JFR 和 JMC 在 Java 8 中都是商业功能，但二者在 Java 11 中都是开放源代码。

### Java 任务控制

java 任务控制 (JMC) 提供 java 网络流量记录器收集的数据的图形显示 (JFR) ，在 java 11 中是开放源代码。除了有关正在运行的应用程序的一般信息外，JMC 还允许用户向下钻取数据。 JFR 和 JMC 可以用来诊断运行时问题，例如内存泄露、GC 开销、热方法、线程瓶颈、阻塞 I/O。

### 统一日志记录

Java 11 有一个通用日志记录系统，适合 JVM 的所有组件。 用户可以使用此统一日志记录系统来定义哪些组件需要记录，以及记录到何种级别。 这种精细的日志记录适用于对 JVM 崩溃进行根本原因分析，以及在生产环境中诊断性能问题。

### 低开销堆分析

已经向 Java 虚拟机工具接口 (JVMTI) 添加了新的 API，用于对 Java 堆分配采样。 采样的开销低，可以持续启用。 虽然可以使用 Java Flight Recorder (JFR) 监视堆分配，但 JFR 中的采样方法只能用于分配。 JFR 实现也可能未命中分配。 与之形成对比的是，Java 11 中的堆采样可以同时提供活对象和死对象的相关信息。

应用程序性能监视 (APM) 供应商开始利用此新功能，Java 工程组正在研究是否有可能将它与 Azure 性能监视工具配合使用。

### StackWalker

进行日志记录时，通常会获取当前线程的堆栈的快照。 问题在于要记录多少堆栈跟踪，以及是否有必要记录堆栈跟踪。 例如，用户可能只想在某个方法出现特定异常时查看堆栈跟踪。 StackWalker 类（在 Java 9 中添加）提供堆栈的快照，并提供方便程序员对堆栈跟踪使用方式进行精细控制的方法。

### 垃圾回收

Java 11 提供以下垃圾回收器：Serial、Parallel、Garbage-First 和 Epsilon。 Java 11 中的默认垃圾回收器是 Garbage First 垃圾回收器 (G1GC)。

此处提到其他三个回收器为了保持内容完整。 Z 垃圾回收器 (ZGC) 是一个并发、低延迟回收器，它会尝试将暂停时间保持在 10 毫秒以下。 ZGC 在 Java 11 中作为实验性功能提供。 Shenandoah 回收器是一个暂停时间短的回收器，它可以通过正在运行的 Java 程序以并发方式进行更多的垃圾回收，因此缩短了 GC 暂停时间。 Shenandoah 是 Java 12 中的一项实验性功能，但可以后向移植到 Java 11。 Concurrent Mark and Sweep (CMS) 回收器已发布，但自 Java 9 发布后已弃用。

对于一般性使用，JVM 会将 GC 用作默认设置。 通常情况下，需根据应用程序的要求对这些设置和其他 GC 设置进行调整，以便优化吞吐量或延迟。 正确调整 GC 需要深入了解 GC，需要 Microsoft Java 工程组提供的专业知识。

### G1GC

Java 11 中的默认垃圾回收器是 G1 垃圾回收器 (G1GC)。 G1GC 的目标是在延迟和吞吐量之间取得平衡。 G1 垃圾回收器尝试在大概率满足暂停时间目标的情况下实现高吞吐量目标。 G1GC 旨在避免整个集合，但当并发回收无法快速回收内存时，将发生回退完全 GC。 完全 GC 使用与初期混合性回收相同的并行工作线程数。

### 并行 GC

并行回收器是 Java 8 中的默认回收器。 并行 GC 是一个吞吐量回收器，使用多个线程来加速垃圾回收。

### Epsilon 


Epsilon 垃圾回收器负责处理分配，但不回收任何内存。 当堆耗尽时，JVM 会关闭。 Epsilon 适用于生存期短的服务和已知没有垃圾的应用程序。

### Docker 容器改进

在 Java 10 之前，JVM 无法识别在容器上设置的内存和 CPU 约束。 例如，在 Java 8 中，JVM 会将最大堆大小默认设置为基础主机物理内存的四分之一。 从 Java 10 开始，JVM 会使用容器控制组 (cgroups) 设置的约束来设置内存和 CPU 限制（参见下面的说明）。 例如，默认的最大堆大小为容器的内存限制的四分之一（例如，如果内存限制为 2G，则最大堆大小为 500MB）。

另外还添加了“JVM 选项”，使 Docker 容器用户可以精细地控制用于 Java 堆的系统内存量。

此支持默认启用，仅在基于 Linux 的平台上提供。




### 多版本 jar 文件

在 Java 11 中，可以创建一个 jar 文件，其中包含多个特定于 Java 发布版的类文件版本。 有了多发布版 jar 文件，库开发人员就可以支持多个 Java 版本，不需交付多个版本的 jar 文件。 对于这些库的使用者来说，多发布版 jar 文件解决了必须将特定 jar 文件与特定运行时目标匹配的问题。

### 其他性能改进

对 JVM 进行以下更改会直接影响性能。

* JEP 197：分段代码缓存——将代码缓存分割成不同的段。 这种分段可以更好地控制 JVM 内存占用、缩短已编译方法的扫描时间、显著减轻代码缓存的碎片化，从而改进性能。
* JEP 254： Compact string——将字符串的内部表示形式从每个字符的两个字节更改为每个字符一个或两个字节，具体取决于字符编码。 由于大多数字符串包含 `ISO-8859-1/拉丁语-1`字符，此更改可以有效地将存储字符串所需的空间量减半。
* JEP 310：应用程序 Class-Data 共享`-Class-Data`共享通过允许在运行时进行内存映射来减少启动时间。 应用程序类-数据共享允许将应用程序类置于 CDS 存档中，从而扩展了类-数据共享。 当多个 JVM 共享同一存档文件时，可以节省内存并缩短总体的系统响应时间。
* JEP 312： Thread-Local 握手——使你能够在无需执行全局 VM safepoint 的情况下在线程上执行回调，这有助于 VM 减少全局 safepoints 的数量，从而实现较低的延迟。
* 延迟分配编译器线程——在分层编译模式下，VM 将启动大量的编译器线程。 在有许多 CPU 的系统上，这是默认模式。 不管可用内存为多少，也不管编译请求有多少个，都会创建这些线程。 线程即使在空闲（几乎所有时间都是如此）的情况下也会耗用内存，这导致资源使用效率不高。 为了解决此问题，我们对实现进行了更改，在启动时每种类型只启动一个编译器线程。 系统会动态处理启动其他线程和关闭未使用线程的操作。

对核心库进行以下更改会影响新代码或已修改代码的性能。

* JEP 193：变量句柄——定义一种标准方法，以调用对象字段和数组元素上的各种 util 和操作的等效操作，这是一组用于精确控制内存排序的标准围栏操作，也是一种标准的可访问性防护操作，以确保引用的对象保持可访问性。
* JEP 269：集合的便利工厂方法——定义库 api，使你可以轻松地创建包含少量元素的集合和映射的实例。 这是集合接口上的静态工厂方法，用于创建精简且不可修改的集合实例。 这些实例本质上更高效。 这些 API 创建的集合以简洁方式表示，没有包装器类。
* JEP 285： Spin-Wait 提示——提供 API，该 API 允许 Java 提示运行时系统处于自旋循环中。 某些硬件平台可以利用表明线程正处于“繁忙-等待”状态的软件指示。
* JEP 321： HTTP 客户端 (标准)——提供一个新的 http 客户端 API，该 API 可实现 Http/2 和 WebSocket，并可替换旧版 HttpURLConnection API。


## Java 8 转换到 Java 11可能的问题


将代码从 Java 8 转换到 Java 11 时，并没有一种适用于所有情况的解决方案。 对于不重要的应用程序来说，从 Java 8 迁移到 Java 11 可能意味着很大的工作量。 潜在问题包括：

* 删除的 API
* 弃用的包
* 内部 API 的使用
* 对类加载程序的更改
* 以及对垃圾回收的更改。

通常，解决方法是尝试在不重新编译的情况下在 Java 11 上运行，或者先使用 JDK 11 进行编译。 如果目标是尽快启动并运行应用程序，则通常情况下，最佳方法是直接在 Java 11 上运行。 对于库，目标将是发布使用 JDK 11 编译和测试的项目。

迁移到 Java 11 值得付出这样的努力。 自 Java 8 发布以来，已添加了多项新功能并对原有功能进行了强化。 这些功能和增强功能可改进启动、性能和内存使用情况，并提供与容器更好的集成。 此外还对 API 进行了添加和修改，这可以提高开发人员的工作效率。


## 工具箱

Java 11 有两个用于探查潜在问题的工具：jdeprscan 和 jdeps。 可以对现有类或 jar 文件运行这两个工具。 无需重新编译即可评估转换工作量。

* jdeprscan 可查看是否使用了已弃用或已删除的 API。 使用已弃用的 API 不是阻塞性问题，但值得探讨。 是否有更新的 jar 文件？ 是否需要记录某个问题才能解决已弃用 API 的使用问题？ 使用已删除的 API 是阻塞性问题，必须予以解决，然后才能尝试在 Java 11 上运行应用程序。
* jdeps，一个 Java 类依赖关系分析器。 与 `--jdk-internals` 选项一起使用时，jdeps 会告诉你哪个类依赖于哪个内部 API。 可以继续使用 Java 11 中的内部 API，但应优先考虑改变这种使用情况。 OpenJDK Wiki 页面 [Java Dependency Analysis Tool](https://wiki.openjdk.java.net/display/JDK8/Java+Dependency+Analysis+Tool)（Java 依赖关系分析工具）推荐了某些常用 JDK 内部 API 的替换项。

Gradle 和 Maven 都有 jdeps 和 jdeprscan 插件。 建议将以下工具添加到生成脚本中。

工具 | Gradle 插件 | Maven 插件
----|---|---
jdeps | jdeps-gradle-plugin | Apache Maven JDeps 插件
jdeprscan | jdeprscan-gradle-plugin | Apache Maven JDeprScan 插件

Java 编译器本身 javac 是工具箱中的另一个工具。 从 jdeprscan 和 jdeps 获取的警告和错误来自编译器。 使用 jdeprscan 和 jdeps 的优点是，可以在现有的 jar 和类文件（包括第三方库）上运行这两个工具。
jdeprscan 和 jdeps 不能做的是对使用反射来访问封装的 API 进行警告。 反射访问在运行时进行检查。 最终必须在 Java 11 上运行代码才能确切地知道。

### 使用 jdeprscan

若要使用 jdeprscan，最简单的方法是为其提供一个来自现有生成的 jar 文件。 还可以为其指定目录（如编译器输出目录）或单个类名。 使用 `--release 11` 选项可获取已弃用 API 的最完整列表。 若要确定要采用的已弃用 API 的优先级，请将设置回退到 `--release 8`。 在 Java 8 中弃用的 API 的删除时间可能会早于最近弃用的 API。

```
jdeprscan --release 11 my-application.jar
```

如果无法解析依赖类，jdeprscan 工具会生成错误消息。 例如 `error: cannot find class org/apache/logging/log4j/Logger`。 建议将依赖类添加到 `--class-path` 或使用应用程序 `class-path`，但该工具会在没有它的情况下继续扫描。 参数是 `-类路径`。 `class-path` 参数的其他变体将不起作用。


```
jdeprscan --release 11 --class-path log4j-api-2.13.0.jar my-application.jar
error: cannot find class sun/misc/BASE64Encoder
class com/company/Util uses deprecated method java/lang/Double::<init>(D)V
```


此输出告诉我们，com.company.Util 类在调用 java.lang.Double 类的已弃用构造函数。 javadoc 会建议用来代替已弃用 API 的 API。 无论如何都无法解决“error: cannot find class sun/misc/BASE64Encoder”问题，因为它是已删除的 API。 自 Java 8 发布以来，应使用 java.util.Base64。

运行 `jdeprscan --release 11 --list` 即可了解自 Java 8 后弃用的具体 API。 若要获取已删除 API 的列表，请运行 `jdeprscan --release 11 --list --for-removal`。

### 使用 jdeps

可以使用 jdeps 通过 `--jdk-internals` 选项来查找 JDK 内部 API 上的依赖项。 此示例需要 `--multi-release 11` 命令行选项，因为 log4j-core-2.13.0.jar 是多版本 jar 文件。 没有此选项，jdeps 会在找到多版本 jar 文件的情况下发出错误消息。 此选项指定要检查的类文件的版本。

```
jdeps --jdk-internals --multi-release 11 --class-path log4j-core-2.13.0.jar my-application.jar
Util.class -> JDK removed internal API
Util.class -> jdk.base
Util.class -> jdk.unsupported
   com.company.Util        -> sun.misc.BASE64Encoder        JDK internal API (JDK removed internal API)
   com.company.Util        -> sun.misc.Unsafe               JDK internal API (jdk.unsupported)
   com.company.Util        -> sun.nio.ch.Util               JDK internal API (java.base)

Warning: JDK internal APIs are unsupported and private to JDK implementation that are
subject to be removed or changed incompatibly and could break your application.
Please modify your code to eliminate dependence on any JDK internal APIs.
For the most recent update on JDK internal API replacements, please check:
https://wiki.openjdk.java.net/display/JDK8/Java+Dependency+Analysis+Tool

JDK Internal API                         Suggested Replacement
----------------                         ---------------------
sun.misc.BASE64Encoder                   Use java.util.Base64 @since 1.8
sun.misc.Unsafe                          See http://openjdk.java.net/jeps/260   
```


输出提供了一些关于避免使用 JDK 内部 API 的好建议！ 如果可能，建议使用替换 API。 在括号中提供封装了包的模块的名称。 如果需要显式中断封装，则可将模块名称与 `--add-exports` 或 `--add-opens` 配合使用。
使用 sun.misc.BASE64Encoder 或 sun.misc.BASE64Decoder 会导致 Java 11 中出现 java.lang.NoClassDefFoundError。 使用这些 API 的代码必须经过修改才能使用 java.util.Base64。

尝试不使用来自 jdk.unsupported 模块的任何 API。 此模块中的 API 将引用 [JDK 增强方案 (JEP) 260](http://openjdk.java.net/jeps/260) 作为建议的替换方案。 简而言之，JEP 260 指出，在替换 API 可用之前，会一直支持使用内部 API。 虽然你的代码使用的是 JDK 内部 API，但至少在一段时间内它是可以正常运行的。 请看看 JEP 260，因为它指出了某些内部 API 的替换项。 例如，可以使用[变量句柄](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/invoke/VarHandle.html)来代替某个 sun.misc.Unsafe API。

除了扫描 JDK 内部 API 的使用情况，jdeps 还可以执行其他操作。 它是一项有用的工具，可以用来分析依赖关系和生成模块信息文件。 有关详细信息，请参阅文档。

### 使用 javac

如果使用 JDK 11 进行编译，则需要更新才能生成脚本、工具、测试框架和包含的库。 使用 javac 的 `-Xlint:unchecked` 选项可获取 JDK 内部 API 的使用详情和其他警告。 可能还需要使用 `--add-opens` 或 `--add-reads` 向编译器公开封装的包（请参阅 JEP 261）。

库可以考虑以多版本 jar 文件形式打包。 多版本 jar 文件允许同时支持同一 jar 文件中的 Java 8 和 Java 11 运行时。 它们增加了生成的复杂性。 如何生成多版本 jar 超出了本文档的讨论范围。

## 在 Java 11 上运行

大多数应用程序在不修改的情况下应该可以在 Java 11 上运行。 首先要尝试的是在不重新编译代码的情况下在 Java 11 上运行。 直接运行的目的是查看执行时会出现哪些警告和错误。 此方法可以让应用程序在 Java 11 上更快地运行，因为可以尽量减少那些必须完成的关注事项。

你可能会遇到的大多数问题都可以得到解决，无需重新编译代码。 如果需要在代码中修复问题，请进行修复，但继续使用 JDK 8 进行编译。 如果可能，请在使用 JDK 11 进行编译之前，让应用程序使用 java 版本 11 运行。

### 检查命令行选项

在 Java 11 上运行之前，请对命令行选项进行快速扫描。 已删除的选项会导致 Java 虚拟机 (JVM) 退出。 如果使用 GC 日志记录选项，则此检查尤其重要，因为它们已明显不同于 Java 8 中的情况。 JaCoLine 工具是一项很好的工具，用于检查命令行选项的问题。

### 检查第三方库

你不能控制的第三方库是潜在的问题来源。 可以主动将第三方库更新到较新的版本。 也可查看运行应用程序时哪些库未使用，仅更新那些必需的库。 将所有库更新到最新版本的问题在于，如果应用程序中存在错误，则更难找到根本原因。 发生此错误是因为更新了某个库吗？ 或者，此错误是由运行时中的某些更改引起的吗？ 仅更新所需内容的问题在于，可能需要多次迭代才能解决问题。

此处的建议是尽可能少做更改，将第三方库单独进行更新。 如果更新第三方库，则往往需要与 Java 11 兼容的最新且最好的版本。 根据当前版本的落后程度，你可能需要采取更谨慎的方法，升级到第一个与 `Java 9+` 兼容的版本。

除了查看发行说明以外，还可以使用 jdeps 和 jdeprscan 来评估 jar 文件。 此外，OpenJDK 质量组还维护一个质量外展 Wiki 页面，其中列出了根据 OpenJDK 版本对多个免费开源软件 (FOSS) 项目进行的测试的状态。

### 显式设置垃圾回收

并行垃圾回收器（并行 GC）是 Java 8 中的默认 GC。 如果应用程序使用默认值，则应使用命令行选项 `-XX:+UseParallelGC` 显式设置 GC。 Java 9 中的默认值已更改为 Garbage First 垃圾回收器 (G1GC)。 若要对 Java 8 与 Java 11 上运行的应用程序进行公平比较，GC 设置必须相同。 应推迟使用 GC 设置进行的试验，直到应用程序在 Java 11 上经过验证。

### 显式设置默认选项

如果在作用点 VM 上运行，则设置命令行选项 `-XX:+PrintCommandLineFlags` 会转储由 VM 设置的选项的值，特别是由 GC 设置的默认值。 在 Java 8 上使用此标志运行，在 Java 11 上运行时使用输出的选项。 大多数情况下，Java 8 到 11 中的默认值是相同的。 但是，使用 Java 8 中的设置可确保奇偶校验。

建议设置命令行选项 `--illegal-access=warn`。 在 Java 11 中，使用反射访问 JDK 内部 API 会生成一个“非法的反射访问”警告。 默认情况下，系统仅对第一次非法访问发出警告。 设置 `--illegal-access=warn` 会导致系统对每一次非法反射访问发出警告。 如果将选项设置为 warn，则会发现更多非法访问案例。 但是，你也会收到大量冗余警告。

在 Java 11 上运行应用程序后，设置 `--illegal-access=deny` 即可模拟 Java 运行时的未来行为。 从 Java 16 开始，默认设置将为 `--illegal-access=deny`。

### ClassLoader 注意事项

在 Java 8 中，可以将系统类加载程序强制转换为 URLClassLoader。 这通常由需要在运行时将类注入到 classpath 的应用程序和库完成。 类加载程序层次结构在 Java 11 中已更改。 系统类加载程序（也称为应用程序类加载程序）现在是一个内部类。 强制转换为 URLClassLoader 会在运行时引发 ClassCastException。 Java 11 无法通过 API 在运行时动态增强 classpath，但可以通过反射来实现这一点，它会显示有关如何使用内部 API 的显著警告。

在 Java 11 中，启动类加载程序只加载核心模块。 如果创建一个具有 null 父项的类加载程序，则它可能找不到全部平台类。 在 Java 11 中，需要在此类情况下传递 ClassLoader.getPlatformClassLoader() 而不是 null 作为父类加载程序。

### 区域设置数据更改

Java 11 中区域设置数据的默认源已通过 [JEP 252](http://openjdk.java.net/jeps/252) 更改为 Unicode 联合会的公共区域设置数据存储库。 这可能会影响本地化的格式设置。 如有必要，请将系统属性设置为 `java.locale.providers=COMPAT,SPI`，还原到 Java 8 区域设置行为。

## 潜在问题

下面是可能会遇到的一些常见问题。 

* 无法识别的 VM 选项
* 无法识别的选项
* VM 警告：忽略选项
* VM 警告：选项 <option> 已弃用
* 警告：发生非法的反射访问操作
* java.lang.reflect.InaccessibleObjectException
* java.lang.NoClassDefFoundError
* -Xbootclasspath/p 不再是受支持的选项
* java.lang.UnsupportedClassVersionError

### 无法识别的选项

如果删除了某个命令行选项，则应用程序会输出 `Unrecognized option:` 或 `Unrecognized VM option`，后跟有问题的选项的名称。 无法识别的选项会导致 VM 退出。 已弃用但未删除的选项会生成 VM 警告。

通常情况下，已删除的选项没有替换项，唯一办法是从命令行中删除该选项。 垃圾回收日志记录的选项是一个例外。 GC 日志记录已在 Java 9 中重新实现，可以使用统一 JVM 日志记录框架。 请参阅 Java SE 11 工具参考的允许[通过 JVM 统一日志记录框架进行日志记录](https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-BE93ABDC-999C-4CB5-A88B-1994AAAC74D5)部分中的“表2-2 将旧的垃圾回收日志记录标志映射到 Xlog 配置”。

### VM 警告
使用弃用的选项会生成警告。 当某个选项被替换或不再有用时，即表明它已被弃用。 与使用删除的选项一样，应从命令行中删除这些选项。 “VM Warning: Option <option> was deprecated”警告意味着，该选项仍受支持，但以后可能会取消该支持。 不再受支持的选项会生成“VM Warning: Ignoring option”警告。 不再受支持的选项不影响运行时。

Web 页面 VM 选项资源管理器提供了自 JDK 7 以后在 Java 中添加或删除的选项的详尽列表。

### 错误：无法创建 Java 虚拟机

当 JVM 遇到无法识别的选项时，会输出此错误消息。

### 警告：发生非法的反射访问操作

当 Java 代码使用反射访问 JDK 内部 API 时，运行时会发出“非法的反射访问”警告。

```
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by my.sample.Main (file:/C:/sample/) to method sun.nio.ch.Util.getTemporaryDirectBuffer(int)
WARNING: Please consider reporting this to the maintainers of com.company.Main
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
```


这意味着，模块未导出通过反射访问的包。 此包在模块中封装，本质上是内部 API。 在 Java 11 上启动并运行应用程序时，第一项操作可能就是忽略此警告。 Java 11 运行时允许反射访问，因此旧代码可以继续运行。
若要解决此警告，请查找不使用内部 API 的已更新代码。 如果无法使用更新的代码解决该问题，则可使用 `--add-exports` 或 `--add-opens` 命令行选项来启用对包的访问权限。 这些选项允许从一个模块访问另一个模块的未导出类型。

`--add-exports`选项允许目标模块访问源模块的命名包的公共类型。 有时，代码会使用 setAccessible(true) 访问非公共成员和 API。 这称为深度反射。 在这种情况下，请使用 `--add-opens`，允许代码访问包的非公共成员。 如果不确定是使用 `--add-exports` 还是 `--add-opens`，请从 `--add-exports` 着手。
应将 `--add-exports` 或 `--add-opens` 选项视为一种权宜解决方案，而不是长期解决方案。 使用这些选项会打破模块系统的封装，该封装是为了防止 JDK 内部 API 被使用。 如果删除或更改内部 API，应用程序会发生故障。 Java 16 会拒绝反射访问，但通过命令行选项（如 `--add-opens`）启用访问的情况除外。 若要模拟未来行为，请在命令行中设置 `--illegal-access=deny`。

发出上述示例中的警告是因为 sun.nio.ch 包不是由 java.base 模块导出的。 换言之，模块 java.base 的 module-info.java 文件中没有 exports sun.nio.ch;。 这可以通过 `--add-exports=java.base/sun.nio.ch=ALL-UNNAMED` 来解决。 未在模块中定义的类隐式属于未命名模块，在字面上命名为 ALL-UNNAMED。

### java.lang.reflect.InaccessibleObjectException

此异常指示你尝试在已封装类的字段或方法上调用 setAccessible(true)。 也可能会收到一个“非法的反射访问”警告。 使用 `--add-opens` 选项可以让代码访问包的非公共成员。 异常消息会告知你，模块未将包打开到试图调用 setAccessible 的模块。 如果模块是“未命名模块”，请使用 UNNAMED-MODULE 作为 `--add-opens` 选项中的目标模块。

```
java.lang.reflect.InaccessibleObjectException: Unable to make field private final java.util.ArrayList jdk.internal.loader.URLClassPath.loaders accessible: 
module java.base does not "opens jdk.internal.loader" to unnamed module @6442b0a6

$ java --add-opens=java.base/jdk.internal.loader=UNNAMED-MODULE example.Main
```


### java.lang.NoClassDefFoundError

NoClassDefFoundError 最有可能是由拆分包或引用删除的模块导致的。

#### 拆分包导致的 NoClassDefFoundError

如果在多个库中找到某个包，则该包为拆分包。 拆分包问题的症状是，你知道某个类会在 class-path 上，但找不到该类。

使用 `module-path` 时才会出现此问题。 Java 模块系统通过将包限制为一个命名的模块来优化类查找。 执行类查找时，运行时会优先处理 `module-path` 而不是 `class-path`。 如果包在某个模块和 `class-path` 之间拆分，则只使用该模块来执行类查找。 这可能导致 NoClassDefFound 错误。

若要检查拆分包，一个简单的方法是将模块路径和类路径插入 jdeps，使用应用程序类文件的路径作为 <path>。 如果有拆分包，jdeps 会输出警告：`Warning: split package: <package-name> <module-path> <split-path>`。
可以通过使用 `--patch-module <module-name>=<path>[,<path>]` 将拆分包添加到命名模块中来解决此问题。

#### 使用 Java EE 或 CORBA 模块导致的 NoClassDefFoundError

如果应用程序在 Java 8 上运行但却引发 java.lang.NoClassDefFoundError 或 java.lang.ClassNotFoundException，则可能是应用程序在使用 Java EE 或 CORBA 模块中的包。 这些模块在 Java 9 弃用，在 Java 11 中删除。

若要解决此问题，请向项目添加运行时依赖项。

删除的模块 | 受影响的包	| 建议的依赖项
---|---|---
Java API for XML Web Services (JAX-WS) |	java.xml.ws	| JAX WS RI 运行时
用于 XML 绑定的 Java 体系结构 (JAXB) |	java.xml.bind |	JAXB 运行时
JavaBeans Activation Framework (JAV) |	java.activation	 |	JavaBeans (TM) Activation Framework
常见批注 |	java.xml.ws.annotation |	Javax 批注 API
通用对象请求代理体系结构 (CORBA) |	java.corba |	GlassFish CORBA ORB
Java 事务 API (JTA) |		java.transaction |	Java 事务 API

### `-Xbootclasspath/p` 不再是受支持的选项

已删除对 `-Xbootclasspath/p` 的支持。 请改用 `--patch-module`。 `--patch-module` 选项在 JEP 261 中介绍。 查找标为“修补模块内容”的部分。 可以将 `--patch-module` 与 javac 和 java 配合使用，以便重写或增强模块中的类。

实际上，`--patch-module` 执行的操作是将修补模块插入模块系统的类查找。 模块系统会首先从修补模块获取类。 这与在 Java 8 中预挂起 bootclasspath 的效果相同。

### UnsupportedClassVersionError

此异常表示你尝试在较低版本的 Java 上运行使用较高版本的 Java 编译的代码。 例如，在 Java 11 上运行其 jar 是使用 JDK 13 编译的程序。

Java 版本 | 类文件格式版本
---|---
8	 |	52
9	 |	53
10	 |	54
11	 |	55
12	 |	56
13	 |	57

## 后续步骤

在 Java 11 上运行应用程序后，请考虑将库移出 `class-path`，然后再将其移入 `module-path`。 查找应用程序所依赖的库的已更新版本。 选择模块库（如果可用）。 尽可能使用 module-path，即使不打算在应用程序中使用模块。 与 `class-path` 相比，使用`module-path` 可以获得更好的类加载性能。

## 参考引用

* 原文同步至：<https://waylau.com/update-from-java-8-to-java-11>
* <https://docs.microsoft.com/zh-cn/java/openjdk/transition-from-java-8-to-java-11>
* 更多示例可见《现代Java案例大全》：<https://github.com/waylau/modern-java-demos>
* 柳伟卫《Java核心编程》：<https://search.jd.com/Search?keyword=%E6%9F%B3%E4%BC%9F%E5%8D%AB%20Java%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B&enc=utf-8&wq=%E6%9F%B3%E4%BC%9F%E5%8D%AB%20Java%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B&pvid=3f8660921bef4700931a735f536eebfb>
