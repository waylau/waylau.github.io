---
layout: post
title: JDK 14发布
date: 2020-03-17 00:22
author: admin
comments: true
categories: [JDK,Java]
tags: [JDK,Java]
---

JDK 14已经于2020年3月17日如期发布。本文介绍JDK 14特性。

<!-- more -->



## JEP 305:	instanceof的模式匹配（预览）

通过对instanceof运算符进行模式匹配来增强Java编程语言。 模式匹配允许程序中的通用逻辑，即从对象中有条件地提取组件，可以更简洁，更安全地表示。 这是JDK 14中的预览语言功能。


### 动机

几乎每个程序都包含某种逻辑，这些逻辑结合了对表达式是否具有某种类型或结构的测试，然后有条件地提取其状态的组件以进行进一步处理。例如，以下是在Java程序中常见的instanceof-and-cast用法：

```java
if (obj instanceof String) {
    String s = (String) obj;
    // 使用s
}
```

上述示例中，为了能够安全地将obj转为我们期望的String类型，需要通过instanceof运算符对obj进行类型判断。这里发生了三件事：

* 测试obj是否是一个String
* 将obj转换为String
* 声明新的局部变量s，以便我们可以使用字符串值。

这种模式很简单，并且所有Java程序员都可以理解，但是由于一些原因，它不是最优的。

* 语法乏味
* 同时执行类型检测和类型转换并不是必要的
* String类型在程序中出现了3次，这混淆了后面更重要的逻辑
* 重复的代码容易滋生错误


在JDK 14中，上述代码可以改为下面的方式：

```java
if (obj instanceof String s) {
    // 使用s
}
```

这样整个代码看上去更加简洁。

### 描述

类型测试模式由指定类型的谓词和单个绑定变量组成。在下面的代码中，短语String是类型测试模式：

```java
if (obj instanceof String s) {
    // 使用s
} else {
    // 不能使用s
}
```


如果obj是String的实例，则将其强制转换为String并分配给绑定变量s。绑定变量在if语句的true块中，而不在if语句的false块中。


与局部变量的范围不同，绑定变量的范围由包含的表达式和语句的语义确定。例如，在此代码中：

```java
if (!(obj instanceof String s)) {
    .. s.contains(..) ..
} else {
    .. s.contains(..) ..
}
```


true块中的s表示封闭类中的字段，false块中的s表示由instanceof运算符引入的绑定变量。

当if语句的条件变得比单个instanceof更复杂时，绑定变量的范围也会相应地增长。 例如，在此代码中：


```java
if (obj instanceof String s && s.length() > 5) {.. s.contains(..) ..}
```


绑定变量s在`&&`运算符右侧以及true块中。仅当instanceof成功并分配给s时，才评估右侧。

另一方面，在此代码中：


```java
if (obj instanceof String s || s.length() > 5) {.. s.contains(..) ..}
```

绑定变量s不在`||`右侧的范围内运算符，也不在true块的范围内。s指的是封闭类中的一个字段。

Joshua Bloch的经典著作*Effective Java*中有一段代码示例：


```java
@Override public boolean equals(Object o) { 
    return (o instanceof CaseInsensitiveString) && 
            ((CaseInsensitiveString) o).s.equalsIgnoreCase(s); 
}
```

这段代码可以使用新的语法写成：

```java
@Override public boolean equals(Object o) { 
    return (o instanceof CaseInsensitiveString cis) &&
            cis.s.equalsIgnoreCase(s); 
}

```

这个特性很有意思，因为它为更为通用的模式匹配打开了大门。模式匹配通过更为简便的语法基于一定的条件来抽取对象的组件，而instanceof刚好是这种情况，它先检查对象类型，然后再调用对象的方法或访问对象的字段。

## JEP 343:	打包工具（孵化）

该特性旨在创建一个用于打包独立Java应用程序的工具。

### 动机

许多Java应用程序需要以一流的方式安装在本机平台上，而不是简单地放置在类路径或模块路径上。对于应用程序开发人员来说，交付简单的JAR文件是不够的。他们必须提供适合本机平台的可安装软件包。这允许以用户熟悉的方式分发，安装和卸载Java应用程序。例如，在Windows上，用户希望能够双击一个软件包来安装他们的软件，然后使用控制面板删除该软件。在macOS上，用户希望能够双击DMG文件并将其应用程序拖到Application文件夹中。

打包工具还可以帮助填补其他技术的空白，例如Java Web Start（已从JDK 11中删除）和pack200（已在JDK 11中弃用，可能在以后的版本中删除）。开发人员可以使用jlink将JDK分解为所需的最小模块集，然后使用打包工具生成一个压缩的、可安装的映像，该映像可以部署到目标计算机。

为了以前满足这些要求，JDK 8分发了一个名为javapackager的打包工具。但是，作为删除JavaFX的一部分，该工具已从JDK 11中删除。

### 描述

jpackage工具将Java应用程序打包到特定于平台的程序包中，该程序包包含所有必需的依赖项。该应用程序可以作为普通JAR文件的集合或作为模块的集合提供。受支持的特定于平台的软件包格式为：

* Linux：deb和rpm
* macOS：pkg和dmg
* Windows：MSI和EXE

默认情况下，jpackage会以最适合其运行系统的格式生成一个软件包。

以下是基本用法：

```
$ jpackage --name myapp --input lib --main-jar main.jar
```

### 用法


#### 1. 基本用法：非模块化应用

假设你有一个由JAR文件组成的应用程序，所有应用程序都位于lib目录下，并且主类在lib/main.jar中。下列命令

```
$ jpackage --name myapp --input lib --main-jar main.jar
```

将以本地系统的默认格式打包应用程序，并将生成的打包文件保留在当前目录中。如果main.jar中的MANIFEST.MF文件没有Main-Class属性，我们必须显式地指定主类：


```
$ jpackage --name myapp --input lib --main-jar main.jar --main-class myapp.Main
```

打包的名称是myapp。要启动该应用程序，启动器将从输入目录复制的每个JAR文件都放在JVM的类路径上。

如果希望生成默认格式以外的软件安装包，可以使用--type选项。例如要在macOS上生成pkg文件（而不是dmg文件），我们可以使用下面的命令：

```
$ jpackage --name myapp --input lib --main-jar main.jar --type pkg
```

#### 2. 基本用法：模块化应用

如果你有一个模块化应用程序，该应用程序由lib目录中的模块化JAR文件和/或JMOD文件组成，并且主类位于myapp模块中，则下面的命令

```
$ jpackage --name myapp --module-path lib -m myapp
```

能够将其打包。如果myapp模块无法识别主类，则必须明确指定：

```
$ jpackage --name myapp --module-path lib -m myapp/myapp.Main
```



## JEP 345:	G1的NUMA内存分配优化

通过实现可识别NUMA的内存分配，提高大型计算机上的G1性能。

### 动机

现代的多插槽计算机越来越多地具有非统一的内存访问（non-uniform memory access,NUMA），即内存与每个插槽或内核之间的距离并不相等。插槽之间的内存访问具有不同的性能特征，对更远的插槽的访问通常具有更大的延迟。

并行收集器中通过启动`-XX:+UseParallelGC`能够感知NUMA，这个功能已经实现了多年了，这有助于提高跨多插槽运行单个JVM的配置的性能。其他HotSpot收集器没有此功能，这意味着他们无法利用这种垂直多路NUMA缩放功能。大型企业应用程序尤其倾向于在多个多插槽上以大堆配置运行，但是它们希望在单个JVM中运行具有可管理性优势。 使用G1收集器的用户越来越多地遇到这种扩展瓶颈。


### 描述

G1的堆组织为固定大小区域的集合。一个区域通常是一组物理页面，尽管使用大页面（通过 `-XX:+UseLargePages`）时，多个区域可能组成一个物理页面。

如果指定了`+XX:+UseNUMA`选项，则在初始化JVM时，区域将平均分布在可用NUMA节点的总数上。

在开始时固定每个区域的NUMA节点有些不灵活，但是可以通过以下增强来缓解。为了为mutator线程分配新的对象，G1可能需要分配一个新的区域。它将通过从NUMA节点中优先选择一个与当前线程绑定的空闲区域来执行此操作，以便将对象保留在新生代的同一NUMA节点上。如果在为变量分配区域的过程中，同一NUMA节点上没有空闲区域，则G1将触发垃圾回收。要评估的另一种想法是，从距离最近的NUMA节点开始，按距离顺序在其他NUMA节点中搜索自由区域。

该特性不会尝试将对象保留在老年代的同一NUMA节点上。

此分配政策中不包括Humongous区。对于这些区，将不做任何特别的事情。

## JEP 349:	JFR事件流


公开JDK Flight Recorder数据以进行连续监视。

### 动机

HotSpot VM通过JFR产生的数据点超过500个，但是使用者只能通过解析日志文件的方法使用它们。

用户要想消费这些数据，必须开始一个记录并停止，将内容转储到磁盘上，然后解析记录文件。这对于应用程序分析非常有效，但是监控数据却十分不方便（例如显示动态更新数据的仪表盘）。

与创建记录相关的开销包括：

* 发出在创建新记录时必须发生的事件
* 写入事件元数据（例如字段布局）
* 写入检查点数据（例如堆栈跟踪）
* 将数据从磁盘存储复制到单独的记录文件

如果有一种方法，可以在不创建新记录文件的情况下，从磁盘存储库中读取正在记录的数据，就可以避免上述开销。

### 描述

jdk.jfr模块里的jdk.jfr.consumer包，提供了异步订阅事件的功能。用户可以直接从磁盘存储库读取记录数据，也可以直接从磁盘存储流中读取数据，而无需转储记录文件。可以通过注册处理器（例如lambda函数）与流交互，从而对事件的到达进行响应。

下面的例子打印CPU的总体使用率，并持有锁10毫秒。

```java
try (var rs = new RecordingStream()) {
  rs.enable("jdk.CPULoad").withPeriod(Duration.ofSeconds(1));
  rs.enable("jdk.JavaMonitorEnter").withThreshold(Duration.ofMillis(10));
  rs.onEvent("jdk.CPULoad", event -> {
    System.out.println(event.getFloat("machineTotal"));
  });
  rs.onEvent("jdk.JavaMonitorEnter", event -> {
    System.out.println(event.getClass("monitorClass"));
  });
  rs.start();
}
```

RecordingStream类实现了接口jdk.jfr.consumer.EventStream，该接口提供了一种统一的方式来过滤和使用事件，无论源是实时流还是磁盘上的文件。


```java
public interface EventStream extends AutoCloseable {
  public static EventStream openRepository();
  public static EventStream openRepository(Path directory);
  public static EventStream openFile(Path file);

  void setStartTime(Instant startTime);
  void setEndTime(Instant endTime);
  void setOrdered(boolean ordered);
  void setReuse(boolean reuse);

  void onEvent(Consumer<RecordedEvent> handler);
  void onEvent(String eventName, Consumer<RecordedEvent handler);
  void onFlush(Runnable handler);
  void onClose(Runnable handler);
  void onError(Runnable handler);
  void remove(Object handler);

  void start();
  void startAsync();

  void awaitTermination();
  void awaitTermination(Duration duration);
  void close();
}
```

创建流的方法有3种:

* `EventStream::openRepository(Path)`从磁盘存储库中构造一个流。这是一种可以直接通过文件系统监视其他进程的方法。磁盘存储库的位置存储在系统属性jdk.jfr.repository中，可以使用API读取到。
* `EventStream::openRepository()`方法执行进程内监控。与RecordingStream不同，它不会开始录制。相反，仅当通过外部方式（例如，使用JCMD或JMX）启动记录时，流才接收事件。
* `EventStream::openFile(Path)`从记录文件中创建流，扩充了已经存在的RecordingFile类。


该接口还可用于设置缓冲的数据量，以及是否应按时间顺序对事件进行排序。为了最大程度地降低分配压力，还可以选择控制是否应为每个事件分配新的事件对象，或者是否可以重用以前的对象。我们可以在当前线程中启动流，也可以异步启动流。

JVM每秒一次将线程本地缓冲区中存储的事件定期刷新到磁盘存储库。 一个单独的线程解析最近的文件，直到写入数据为止，然后将事件推送给订阅者。 为了保持较低的开销，仅从文件中读取活动订阅的事件。 要在刷新完成后收到通知，可以使用`EventStream::onFlush(Runnable)`方法注册处理程序。 这是在JVM准备下一组事件时将数据聚合或推送到外部系统的机会。
 

## JEP 352:	非易失性映射字节缓冲区


添加新的特定于JDK的文件映射模式，以便可以使用FileChannel API创建引用非易失性内存（non-volatile memory，NVM）的MappedByteBuffer实例。


### 动机


NVM为应用程序程序员提供了在程序运行过程中创建和更新程序状态的机会，而减少了输出到持久性介质或从持久性介质输入时的成本。这对于事务程序特别重要，在事务程序中，需要定期保持不确定状态以启用崩溃恢复。

现有的C库（例如Intel的libpmem）为C程序提供了对基层NVM的高效访问。他们还以此为基础来支持对各种持久性数据类型的简单管理。当前，由于频繁需要进行系统调用或JNI调用来调用原始操作，从而确保内存更改是持久的，因此即使仅使用Java中的基础库也很昂贵。同样的问题限制了高级库的使用，并且由于C中提供的持久数据类型分配在无法从Java直接访问的内存中这一事实而加剧了这一问题。与C或可以低成本链接到C库的语言相比，这使Java应用程序和中间件（例如Java事务管理器）处于严重的劣势。

该特性试图通过允许映射到ByteBuffer的NVM的有效写回来解决第一个问题。由于Java可以直接访问ByteBuffer映射的内存，因此这可以通过实现与C语言中提供的客户端库等效的客户端库来解决第二个问题，以管理不同持久数据类型的存储。

### 描述

#### 1. 初步变更

该JEP使用了Java SE API的两个增强功能：

* 支持implementation-defined的映射模式
* MppedByteBuffer::force方法以指定范围

#### 2. 特定于JDK的API更改

* 通过新模块中的公共API公开新的MapMode枚举值

一个公共扩展枚举ExtendedMapMode将添加到jdk.nio.mapmode程序包：

```java
package jdk.nio.mapmode;
. . .
public class ExtendedMapMode {
    private ExtendedMapMode() { }

    public static final MapMode READ_ONLY_SYNC = . . .
    public static final MapMode READ_WRITE_SYNC = . . .
}
```

在调用`FileChannel::map`方法创建映射到NVM设备文件上的只读或读写MappedByteBuffer时，可以使用上述的枚举值。如果这些标志在不支持NVM设备文件的平台上传递，程序会抛出UnsupportedOperationException异常。在受支持的平台上，仅当目标FileChannel实例是从通过NVM设备打开的派生文件时，才能传递这些参数。在任何其他情况下，都会抛出IOException异常。


* 发布BufferPoolMXBean，用于跟踪MappedByteBuffer统计信息


## JEP 358:	友好的空指针异常

精确描述哪个变量为null，提高JVM生成的NullPointerException的可用性。

### 动机

每个Java开发人员都遇到过NullPointerException（NPE）问题。NPE几乎可以出现在程序的任意位置，因此尝试捕获和修复它们是不可能的。下面的代码：

```java
a.i = 99;
```

JVM会打印出方法名、文件名和NPE异常的行数：

```
Exception in thread "main" java.lang.NullPointerException
    at Prog.main(Prog.java:5)
```


使用这个错误报告，开发人员可以定位到`a.i = 99;`并推断对象a是null。但是对于更复杂的代码，不使用调试器就无法确定哪个变量为空。假设下面的代码中出现了一个NPE：

```java
a.b.c.i = 99;
```

仅仅使用文件名和行数，并不能精确定位到哪个变量为null，是a、b还是c？

访问数组也会发生类似的问题。假设此代码中出现一个NPE：

```java
a[i][j][k] = 99;
```

文件名和行号不能精确指出哪个数组组件为空。是a还是`a[i]`或`a[i][j]`？

一行代码可能包含多个访问路径，每个访问路径都可能是NPE的来源。假设此代码中出现一个NPE：

```java
a.i = b.j;
```

文件名和行号并不能确定哪个对象为空，是a还是b？

NPE也可能在方法调用中传递，看下面的代码：

```java
x().y().i = 99;
```

文件名和行号不能指出哪个方法调用返回null。是x()还是y()？

### 描述

JVM在程序调用空引用的位置抛出NPE异常，通过分析程序的字节码指令，JVM可以精确判断哪个变量为空，并在NPE中描述详细信息（根据源代码）。包含方法名、文件名和行号的null-detail消息将显示在JVM的消息中。

例如`a.i = 99;`的NPE异常可能是如下格式：

```
Exception in thread "main" java.lang.NullPointerException: 
        Cannot assign field "i" because "a" is null
    at Prog.main(Prog.java:5)
```

在更复杂的`a.b.c.i = 99;`语句中，NPE消息会包含导致空值的完整访问路径：

```
Exception in thread "main" java.lang.NullPointerException: 
        Cannot read field "c" because "a.b" is null
    at Prog.main(Prog.java:5)
```

同样，如果数组访问和赋值语句`a[i][j][k] = 99;`引发NPE：

```
Exception in thread "main" java.lang.NullPointerException:
        Cannot load from object array because "a[i][j]" is null
    at Prog.main(Prog.java:5)
```

类似地，`a.i = b.j;`会引发NPE：

```
Exception in thread "main" java.lang.NullPointerException:
        Cannot read field "j" because "b" is null
    at Prog.main(Prog.java:5)
```




## JEP 359:	record（预览）

通过record增强Java编程语言。record提供了一种紧凑的语法来声明类，这些类是浅层不可变数据的透明持有者。


### 动机


我们经常听到这样的抱怨：“Java太冗长”、“Java规则过多”。首当其冲的就是充当简单集合的“数据载体”的类。为了写一个数据类，开发人员必须编写许多低价值、重复且容易出错的代码：构造函数、访问器、equals()、hashCode()和toString()等等。

尽管IDE可以帮助开发人员编写数据载体类的绝大多数编码，但是这些代码仍然冗长。

从表面上看，将Record是为了简化模板编码而生的，但是它还有“远大”的目标：modeling data as data（将数据建模为数据）。record应该更简单、简洁、数据不可变。

### 描述

record是Java的一种新的类型。同枚举一样，record也是对类的一种限制。record放弃了类通常享有的特性：将API和表示解耦。但是作为回报，record使数据类变得非常简洁。

一个record具有名称和状态描述。状态描述声明了record的组成部分。例如：


```java
record Point(int x, int y) { }
```

因为record在语义上是数据的简单透明持有者，所以记录会自动获取很多标准成员：

* 状态声明中的每个成员，都有一个 private final的字段；
* 状态声明中的每个组件的公共读取访问方法，该方法和组件具有相同的名字；
* 一个公共的构造函数，其签名与状态声明相同；
* equals和hashCode的实现；
* toString的实现。




### 限制

records不能扩展任何类，并且不能声明私有字段以外的实例字段。声明的任何其他字段都必须是静态的。

records类都是隐含的final类，并且不能是抽象类。这些限制使得records的API仅由其状态描述定义，并且以后不能被其他类实现或继承。

### 在record中额外声明变量

也可以显式声明从状态描述自动派生的任何成员。可以在没有正式参数列表的情况下声明构造函数（这种情况下，假定与状态描述相同），并且在正常构造函数主体正常完成时调用隐式初始化（this.x=x）。这样就可以在显式构造函数中仅执行其参数的验证等逻辑，并省略字段的初始化，例如：

```java
record Range(int lo, int hi) {
  public Range {
    if (lo > hi)  /* referring here to the implicit constructor parameters */
      throw new IllegalArgumentException(String.format("(%d,%d)", lo, hi));
  }
}
```



## JEP 361:	Switch Expressions （标准）

扩展switch可以使其应用于语句或表达式。扩展switch使其可以用作语句或表达式，以便两种形式都可以使用传统的“case ... :”标签（带有贯穿）或“... ->”标签（不带有贯穿），还有另一个新语句，用于从switch表达式产生值。这些更改将简化日常编码，并为在交换机中使用模式匹配提供了方法。这些功能在JDK 12和JDK 13中是属于预览语言功能，在JDK 14中正式称为标准。

### 动机

当我们准备增强Java编程语言以支持模式匹配（JEP 305）时，现有switch语句的一些不规则性（长期以来一直困扰着用户）成为了障碍。下面的代码中，众多的break语句使代码变得冗长，这种“视觉噪声”通常掩盖了更多的错误。

```java
switch (day) {
    case MONDAY:
    case FRIDAY:
    case SUNDAY:
        System.out.println(6);
        break;
    case TUESDAY:
        System.out.println(7);
        break;
    case THURSDAY:
    case SATURDAY:
        System.out.println(8);
        break;
    case WEDNESDAY:
        System.out.println(9);
        break;
}
```

我们建议引入一种新形式的switch标签“case L ->”，以表示如果匹配标签，则只执行标签右边的代码。switch标签允许在每种情况下使用逗号分隔多个常量。现在可以这样编写以前的代码：

```java
switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> System.out.println(6);
    case TUESDAY                -> System.out.println(7);
    case THURSDAY, SATURDAY     -> System.out.println(8);
    case WEDNESDAY              -> System.out.println(9);
}
```

switch标签“case L ->”右侧的代码被限制为表达式、代码块或throw语句。这样局部变量的范围在本块之内，而传统的switch语句局部变量的作用域是整个模块！

```java
switch (day) {
    case MONDAY:
    case TUESDAY:
        int temp = ...     // The scope of 'temp' continues to the }
        break;
    case WEDNESDAY:
    case THURSDAY:
        int temp2 = ...    // Can't call this variable 'temp'
        break;
    default:
        int temp3 = ...    // Can't call this variable 'temp'
}
```

许多现有的switch语句实质上是对switch表达式的模拟，其中每个分支要么分配给一个公共目标变量，要么返回一个值：

```java
int numLetters;
switch (day) {
    case MONDAY:
    case FRIDAY:
    case SUNDAY:
        numLetters = 6;
        break;
    case TUESDAY:
        numLetters = 7;
        break;
    case THURSDAY:
    case SATURDAY:
        numLetters = 8;
        break;
    case WEDNESDAY:
        numLetters = 9;
        break;
    default:
        throw new IllegalStateException("Wat: " + day);
}
```

上面的表述是复杂、重复且容易出错的。代码设计者的意图是为每天计算numLetters。这段代码可以改写成下面这段形式，更加清晰和安全：

```java
int numLetters = switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> 6;
    case TUESDAY                -> 7;
    case THURSDAY, SATURDAY     -> 8;
    case WEDNESDAY              -> 9;
};
```

### 描述

#### 1. “case L ->”标签

除了传统的“case L :”标签外，还定义了一种更简洁的形式：“case L ->”标签。如果表达式匹配了某个标签，则仅执行箭头右侧的表达式或语句；否则将不执行任何操作。

```java
static void howMany(int k) {
    switch (k) {
        case 1  -> System.out.println("one");
        case 2  -> System.out.println("two");
        default -> System.out.println("many");
    }
}
```

下面的代码：

```java
howMany(1);
howMany(2);
howMany(3);
```

将会打印：

```
one
two
many
```

#### 2. Switch表达式

JDK 14扩展了switch语句，使其可以应用于表达式中。例如上述的howMany方法可以重写为如下形式：

```java
static void howMany(int k) {
    System.out.println(
        switch (k) {
            case  1 -> "one";
            case  2 -> "two";
            default -> "many";
        }
    );
}
```

在通常情况下，switch表达式如下所示：

```java
T result = switch (arg) {
    case L1 -> e1;
    case L2 -> e2;
    default -> e3;
};
```

#### 3. 通过yield产生值

大多数switch表达式在“case L->”标签的右侧都有一个表达式。如果需要一个完整的块，JDK 14引入了一个新的yield语句来产生一个值，该值成为封闭的switch表达式的值。

```java
int j = switch (day) {
    case MONDAY  -> 0;
    case TUESDAY -> 1;
    default      -> {
        int k = day.toString().length();
        int result = f(k);
        yield result;
    }
};
```


## JEP 362:	弃用Solaris和SPARC端口

不建议使用Solaris/SPARC，Solaris/x64和Linux/SPARC端口，以在将来的发行版中删除它们。

### 动机

放弃对这些端口的支持将使OpenJDK社区中的贡献者能够加速新功能的开发，这些新功能将推动平台向前发展。


## JEP 363:	移除CMS垃圾收集器

移除CMS（Concurrent Mark Sweep）垃圾收集器。


### 动机

在两年多以前的JEP 291中，就已经弃用了CMS收集器，并说明会在以后的发行版中删除，以加快其他垃圾收集器的发展。在这段时间里，我们看到了2个新的垃圾收集器ZGC和Shenandoah的诞生，同时对G1的进一步改进。G1自JDK 6开始便成为CMS的继任者。我们希望以后现有的收集器进一步减少对CMS的需求。

### 描述

此更改将禁用CMS的编译，删除源代码中`gc/cms`目录的内容，并删除仅与CMS有关的选项。尝试使用命令`-XX:+UseConcMarkSweepGC`开启CMS会收到以下警告：

```
Java HotSpot(TM) 64-Bit Server VM warning: Ignoring option UseConcMarkSweepGC; \
support was removed in <version>
```


VM将使用默认收集器继续执行。



## JEP 364:	macOS系统上的ZGC（实验）

将ZGC垃圾收集器移植到macOS。

### 动机

尽管我们希望需要ZGC可伸缩性的用户使用基于Linux的环境，但是在部署应用程序之前，开发人员通常会使用Mac进行本地开发和测试。 还有一些用户希望运行桌面应用程序，例如带有ZGC的IDE。


### 描述

ZGC的macOS实现由两部分组成：

* 支持macOS上的多映射内存。 ZGC设计大量使用彩色指针，因此在macOS上我们需要一种将多个虚拟地址（在算法中包含不同颜色）映射到同一物理内存的方法。我们将为此使用mach microkernel mach_vm_remap API。堆的物理内存在单独的地址视图中维护，在概念上类似于文件描述符，但位于（主要是）连续的虚拟地址中。该内存被重新映射到内存的各种ZGC视图中，代表了算法的不同指针颜色。
* ZGC支持不连续的内存保留。在Linux上，我们在初始化期间保留16TB的虚拟地址空间。我们假设没有共享库将映射到所需的地址空间。在默认的Linux配置上，这是一个安全的假设。但是在macOS上，ASLR机制会侵入我们的地址空间，因此ZGC必须允许堆保留不连续。假设VM实现使用单个连续的内存预留，则共享的VM代码也必须停止。如此一来，is_in_reserved()，reserved_region()和base()之类的GC API将从CollectedHeap中删除。




## JEP 365:	Windows系统上的ZGC（实验）

将ZGC垃圾收集器移植到Windows系统上。


### 描述

ZGC的大多数代码库都是平台无关的，不需要Windows特定的更改。现有的x64负载屏障支持与操作系统无关，也可以在Windows上使用。需要移植的特定于平台的代码与如何保留地址空间以及如何将物理内存映射到保留的地址空间有关。用于内存管理的Windows API与POSIX API不同，并且在某些方面不太灵活。

Windows实现的ZGC需要进行以下工作：

* 支持多映射内存。 ZGC使用彩色指针需要支持堆多重映射，以便可以从进程地址空间中的多个不同位置访问同一物理内存。在Windows上，分页文件支持的内存为物理内存提供了一个标识（句柄），该标识与映射它的虚拟地址无关。使用此标识，ZGC可以将同一物理内存映射到多个位置。
* 支持将分页文件支持的内存映射到保留的地址空间。 Windows内存管理API不如POSIX的mmap/munmap灵活，尤其是在将文件支持的内存映射到以前保留的地址空间区域中时。为此，ZGC将使用Windows概念的地址空间占位符。 Windows 10和Windows Server版本1803中引入了占位符概念。不会实现对Windows较早版本的ZGC支持。
* 支持映射和取消映射堆的任意部分。 ZGC的堆布局与其动态调整堆页面大小（以及重新调整大小）相结合，需要支持映射和取消映射任意堆粒子。此要求与Windows地址空间占位符结合使用时，需要特别注意，因为占位符必须由程序显式拆分/合并，而不是由操作系统自动拆分/合并（如在Linux上）。
* 支持提交和取消提交堆的任意部分。 ZGC可以在Java程序运行时动态地提交和取消提交物理内存。为了支持这些操作，物理内存将被划分为多个分页文件段并由其支持。每个分页文件段都对应一个ZGC堆粒度，并且可以独立于其他段进行提交和取消提交。

## JEP 366:	弃用Parallel Scavenge和Serial Old垃圾收集算法的组合

弃用Parallel Scavenge和Serial Old垃圾收集算法的组合。


### 动机

有一组GC算法的组合很少使用，但是维护起来却需要巨大的工作量：并行年轻代GC（ParallelScavenge）和串行老年代GC（SerialOld）的组合。用户必须使用`-XX:+UseParallelGC -XX:-UseParallelOldGC`来启用此组合。

这种组合是畸形的，因为它将并行的年轻代GC算法和串行的老年代GC算法组合在一起使用。我们认为这种组合仅在年轻代很多、老年代很少时才有效果。在这种情况下，由于老年代的体积较小，因此完整的收集暂停时间是可以接受的。但是在生产环境中，这种方式是非常冒险的：年轻代的对象容易导致OutOfMemoryException。此组合的唯一优势是总内存使用量略低。我们认为，这种较小的内存占用优势（最多是Java堆大小的约3％）不足以超过维护此GC组合的成本。

### 描述


除了弃用选项组合`-XX:+UseParallelGC -XX:-UseParallelOldGC`外，我们还将弃用选项`-XX:UseParallelOldGC`，因为它唯一的用途是取消选择并行的旧版GC，从而启用串行旧版GC。

因此，任何对UseParallelOldGC选项的明确使用都会显示弃用警告。




## JEP 367:	移除Pack200工具和API

删除java.util.jar软件包中的pack200和unpack200工具以及Pack200 API。这些工具和API在Java SE 11中已经被注明为不推荐，并明确打算在将来的版本中删除它们。

### 动机

Pack200是JSR 200在Java SE 5.0中引入的一种JAR文件压缩方案。其目标是“减少Java应用程序打包，传输和交付的磁盘和带宽需求”。开发人员使用一对工具pack200和unpack200压缩和解压缩其JAR文件。在java.util.jar包中提供了一个API。

删除Pack200的三个原因：

* 从历史上看，通过56k调制解调器缓慢下载JDK阻碍了Java的采用。 JDK功能的不断增长导致下载量膨胀，进一步阻碍了采用。使用Pack200压缩JDK是缓解此问题的一种方法。但是，时间已经过去了：下载速度得到了提高，并且JDK 9为Java运行时（JEP 220）和用于构建运行时的模块（JMOD）引入了新的压缩方案。因此，JDK 9和更高版本不依赖Pack200。 JDK 8是在构建时用pack200压缩的最新版本，在安装时用unpack200压缩的最新版本。总之，Pack200的主要使用者（JDK本身）不再需要它。
* 除了JDK，Pack200还可以压缩客户端应用程序，尤其是applet。某些部署技术（例如Oracle的浏览器插件）会自动解压缩applet JAR。但是，客户端应用程序的格局已经改变，并且大多数浏览器都放弃了对插件的支持。因此，Pack200的主要消费者类别（在浏览器中运行的小程序）不再是将Pack200包含在JDK中的驱动程序。
* Pack200是一项复杂而精致的技术。它的文件格式与类文件格式和JAR文件格式紧密相关，二者均以JSR 200所无法预料的方式发展。（例如，JEP 309向类文件格式添加了一种新的常量池条目，并且JEP 238在JAR文件格式中添加了版本控制元数据。）JDK中的实现是在Java和本机代码之间划分的，这使得维护变得很困难。 java.util.jar.Pack200中的API不利于Java SE平台的模块化，从而导致在Java SE 9中删除了其四种方法。总的来说，维护Pack200的成本是巨大的，并且超过了其收益。包括在Java SE和JDK中。

### 描述

JDK最终针对的JDK功能发行版中将删除以前用`@Deprecated(forRemoval = true)`注解的java.base模块中的三种类型：

* java.util.jar.Pack200
* java.util.jar.Pack200.Packer
* java.util.jar.Pack200.Unpacker

包含pack200和unpack200工具的jdk.pack模块先前已使用`@Deprecated(forRemoval = true)`进行了注解，并且还将在此JEP最终针对的JDK功能版本中将其删除。


## JEP 368:	Text Blocks（二次预览）

Java语言增加文本块功能。文本块是多行字符串文字，能避免大多数转义。

### 动机

在Java中，HTML, XML, SQL, JSON等字符串对象都很难阅读和维护。

#### 1. HTML

使用one-dimensional的字符串语法：

```java
String html = "<html>\n" +
              "    <body>\n" +
              "        <p>Hello, world</p>\n" +
              "    </body>\n" +
              "</html>\n";
```


使用two-dimensional文本块语法：

```java
String html = """
              <html>
                  <body>
                      <p>Hello, world</p>
                  </body>
              </html>
              """;
```

#### 2. SQL

使用one-dimensional的字符串语法：


```java
String query = "SELECT `EMP_ID`, `LAST_NAME` FROM `EMPLOYEE_TB`\n" +
               "WHERE `CITY` = 'INDIANAPOLIS'\n" +
               "ORDER BY `EMP_ID`, `LAST_NAME`;\n";
```
               
使用two-dimensional文本块语法：

```java
String query = """
               SELECT `EMP_ID`, `LAST_NAME` FROM `EMPLOYEE_TB`
               WHERE `CITY` = 'INDIANAPOLIS'
               ORDER BY `EMP_ID`, `LAST_NAME`;
               """;
```

#### 3. 多语言示例

使用one-dimensional的字符串语法：

```java
ScriptEngine engine = new ScriptEngineManager().getEngineByName("js");
Object obj = engine.eval("function hello() {\n" +
                         "    print('\"Hello, world\"');\n" +
                         "}\n" +
                         "\n" +
                         "hello();\n");
```

使用two-dimensional文本块语法：


```java
ScriptEngine engine = new ScriptEngineManager().getEngineByName("js");
Object obj = engine.eval("""
                         function hello() {
                             print('"Hello, world"');
                         }
                         
                         hello();
                         """);
```

### 描述

文本块是Java语言的新语法，可以用来表示任何字符串，具有更高的表达能力和更少的复杂度。

文本块的开头定界符是由三个双引号字符（"""）组成的序列，后面跟0个或多个空格，最后跟一个行终止符。内容从开头定界符的行终止符之后的第一个字符开始。

结束定界符是三个双引号字符的序列。内容在结束定界符的第一个双引号之前的最后一个字符处结束。

与字符串文字中的字符不同，文本块的内容中可以直接包含双引号字符。允许在文本块中使用`\"`，但不是必需的或不建议使用。

与字符串文字中的字符不同，内容可以直接包含行终止符。允许在文本块中使用`\n`，但不是必需或不建议使用。例如，文本块：

```java
"""
line 1
line 2
line 3
"""
```

等效于字符串文字：

```java
"line 1\nline 2\nline 3\n"
```

或字符串文字的串联：

```java
"line 1\n" +
"line 2\n" +
"line 3\n"
```

## JEP 370:	外部存储器API（孵化）


引入一个API，以允许Java程序安全有效地访问Java堆之外的外部内存。

### 动机

许多Java的库都能访问外部存储，例如Ignite、mapDB、memcached及Netty的ByteBuf API。这样可以：

* 避免垃圾回收相关成本和不可预测性
* 跨多个进程共享内存
* 通过将文件映射到内存中来序列化、反序列化内存内容。

但是Java API却没有提供一个令人满意的访问外部内存的解决方案。

Java 1.4中引入的ByteBuffer API允许创建直接字节缓冲区，这些缓冲区是按堆外分配的，并允许用户直接从Java操作堆外内存。但是，直接缓冲区是有限的。

开发人员可以从Java代码访问外部内存的另一种常见途径是使用sun.misc.Unsafe API。Unsafe有许多公开的内存访问操作（例如Unsafe::getInt和putInt）。使用Unsafe访问内存非常高效：所有内存访问操作都定义为JVM内在函数，因此JIT会定期优化内存访问操作。然而根据定义，Unsafe API是不安全的——它允许访问任何内存位置（例如，`Unsafe::getInt`需要很长的地址）。如果Java程序了访问某些已释放的内存位置，可能会使JVM崩溃。最重要的是，Unsafe API不是受支持的Java API，并且强烈建议不要使用它。

虽然也可以使用JNI访问内存，但是与该解决方案相关的固有成本使其在实践中很少适用。整个开发流程很复杂，因为JNI要求开发人员编写和维护C代码段。 JNI本质上也很慢，因为每次访问都需要Java到native的转换。

在访问外部内存时，开发人员面临一个难题：应该使用安全但受限（可能效率较低）的方法（例如ByteBuffer），还是应该放弃安全保证并接受不受支持和危险的Unsafe API？

该JEP引入了受支持的，安全且有效的外部内存访问API。并且设计时就充分考虑了JIT优化。

### 描述

外部存储器访问API引入了三个主要的抽象：MemorySegment，MemoryAddress和MemoryLayout。

MemorySegment用于对具有给定空间和时间范围的连续内存区域进行建模。可以将MemoryAddress视为段内的偏移量，MemoryLayout是内存段内容的程序描述。

可以从多种来源创建内存段，例如本机内存缓冲区，Java数组和字节缓冲区（直接或基于堆）。例如，可以如下创建本机内存段：

```java
try (MemorySegment segment = MemorySegment.allocateNative(100)) {
   ...
}
```

上述代码将创建大小为100字节的，与本机内存缓冲区关联的内存段。

内存段在空间上受限制；任何试图使用该段来访问这些界限之外的内存的尝试都会导致异常。正如使用try-with-resource构造所证明的那样，片段在时间上也是有界的。也就是说，它们已创建，使用并在不再使用时关闭。关闭段始终是一个显式操作，并且可能导致其他副作用，例如与该段关联的内存的重新分配。任何访问已关闭的内存段的尝试都将导致异常。空间和时间安全性检查对于确保内存访问API的安全性至关重要。

通过获取内存访问var句柄可以取消引用与段关联的内存。这些特殊的var句柄具有至少一个强制访问坐标，类型为MemoryAddress，即发生取消引用的地址。它们是使用MemoryHandles类中的工厂方法获得的。要设置本机段的元素，我们可以使用如下所示的内存访问var句柄：

```java
VarHandle intHandle = MemoryHandles.varHandle(int.class);

try (MemorySegment segment = MemorySegment.allocateNative(100)) {
   MemoryAddress base = segment.baseAddress();
   for (int i = 0 ; i < 25 ; i++) {
        intHandle.set(base.offset(i * 4), i);
   }
}
```


## 参考引用

* 本文同步至: <https://waylau.com/jdk-14-released/>
* https://openjdk.java.net/projects/jdk/14/
