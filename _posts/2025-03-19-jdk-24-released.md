---
layout: post
title: JDK 24发布
date: 2025-03-19 00:22
author: admin
comments: true
categories: [JDK,Java]
tags: [JDK,Java]
---

JDK 24已经于2025年3月18日正式发布。本文总结了JDK 24发布的新特性。

<!-- more -->



## 安装包下载

主要分为OpenJDK版本和Oracle版本，下载地址如下：

* OpenJDK版本：<https://jdk.java.net/24/>
* Oracle版本：<https://www.oracle.com/java/technologies/downloads/>


上述版本，如果是个人学习用途，则差异不大。但如果是用于商业用途，则需要仔细看好相关的授权。Oracle JDK商用需要遵循 [Oracle No-Fee Terms and Conditions (NFTC)](https://www.oracle.com/downloads/licenses/javase-license1.html)协议，而OpenJDK根据GPL v2许可获得许可。 


更多有关Java的基本知识，可以参阅《[Java核心编程](https://item.jd.com/12868796.html)》这本书，描述的非常详细。


## JDK 24 新特性说明


### 404:	Generational Shenandoah (Experimental)


该JEP主要是提供了一个实验性的分代模式，与其他分代收集器一样分为年轻代和年老代，可以使用如下命令开启Shenandoah的分代模式：

```
-XX:+UnlockExperimentalVMOptions -XX:ShenandoahGCMode=generational
```

### 450:	Compact Object Headers (Experimental)

该特性在64位架构上，将 HotSpot JVM中的对象标头大小从96到128位减少到64位。Java程序中的对象往往很小，作为 Project Lilliput的一部分进行的实验表明，许多工作负载的平均对象大小为 256 到 512 位（32 到 64 字节）。这意味着超过 20% 的实时数据可以单独由对象标头获取。因此，即使对象标头大小略有改进，也可以显著减少占用空间、数据局部性并减轻 GC 压力。在实际应用程序中试用过Project Lilliput的早期采用者证实，内存占用通常会减少10%–20%。可以通过如下命令开启：


```
-XX:+UnlockExperimentalVMOptions -XX:+UseCompactObjectHeaders
```

### 472:	Prepare to Restrict the Use of JNI



该特性主要是默认会对通过JNI或者FFM去访问native code发出warnings，为后续默认限制访问做准备。可以通过开启native access来关闭warnings，命令如下：

```
java --enable-native-access=ALL-UNNAMED ...
java --enable-native-access=M1,M2,... ...
```
### 475:	Late Barrier Expansion for G1

该特性主要是将Late Barrier Expansion引进到G1中。Barrier expansion是指在垃圾回收过程中插入或生成额外代码（称为“屏障”）以管理内存并确保垃圾回收的正确性。这些屏障通常被插入到字节码中的特定位置，例如在内存访问之前或之后，以执行以下任务：

* 记住写操作：跟踪对象的更改，这有助于垃圾回收器识别需要扫描的堆的部分。例如，写屏障（write barrier）会在每次存储操作之前执行，记录哪些对象被修改，从而帮助垃圾回收器维护对象的可达性信息。
* 保持一致性：确保程序对内存的视图与垃圾回收器的视图保持一致，特别是在并发垃圾回收阶段。例如，读屏障（read barrier）会在读取操作之前检查指针是否指向堆内存，并记录这些操作，以防止垃圾回收器误判对象的可达性。
* 处理引用：管理对象之间的引用，特别是在压缩或迁移阶段。例如，在垃圾回收的增量收集中，屏障可以阻止指向未分配空间（fromspace）的指针进入寄存器，从而避免垃圾回收器无法追踪到这些对象。

Early barrier expansion的含义是这些屏障在编译过程的早期插入或生成，而如果在过程的后期进行（正如JEP所提议的），则可以实现更优化的放置，并可能减少这些屏障相关的开销，这可以带来性能的提升和更高效的垃圾回收。

### 478:	Key Derivation Function API (Preview)


随着量子计算领域的进步，传统加密算法变得更容易受到实际攻击。因此，Java平台必须整合后量子密码学（PQC），以抵御这些威胁。Java的长期目标是最终实现混合公钥加密（HPKE），以便无缝过渡到量子安全加密。JDK 21中包含的KEM API（JEP 452）是HPKE的一个组成部分，标志着Java朝着HPKE迈出的第一步，并为后量子挑战做好了准备。该JEP提出了HPKE的另一个组成部分，作为这一方向上的下一步：密钥派生函数（KDFs）的API。使用示例如下：


```java
// Create a KDF object for the specified algorithm
KDF hkdf = KDF.getInstance("HKDF-SHA256"); 

// Create an ExtractExpand parameter specification
AlgorithmParameterSpec params =
    HKDFParameterSpec.ofExtract()
                     .addIKM(initialKeyMaterial)
                     .addSalt(salt).thenExpand(info, 32);

// Derive a 32-byte AES key
SecretKey key = hkdf.deriveKey("AES", params);

// Additional deriveKey calls can be made with the same KDF object
```

### 479:	Remove the Windows 32-bit x86 Port


在JDK21的JEP 449: Deprecate the Windows 32-bit x86 Port for Removal，已经废弃了对Windows 32位 x86的移植，本次JDK24删除了相关源代码、移除了对相关构建支持。


### 483:	Ahead-of-Time Class Loading & Linking


当 HotSpot Java 虚拟机启动时，通过使应用程序的类立即可用，使其处于加载和链接状态，从而缩短启动时间。通过在一次运行期间监视应用程序并将所有类的加载和链接形式存储在缓存中以供后续运行使用来实现此目的。为将来改进启动和预热时间奠定基础。该特性通过Ahead-of-Time Cache来存储已经读取、解析、加载和链接的类。

首先运行application来记录AOT配置:

```
java -XX:AOTMode=record -XX:AOTConfiguration=app.aotconf -cp app.jar com.example.App ...
```

接着使用该配置来创建AOT缓存：

```
java -XX:AOTMode=create -XX:AOTConfiguration=app.aotconf -XX:AOTCache=app.aot -cp app.jar
```

最后使用AOT缓存启动：

```
java -XX:AOTCache=app.aot -cp app.jar com.example.App ...
```

AOT缓存将读取、解析、加载和链接（通常在程序执行期间即时完成）的任务提前到缓存创建的早期阶段。因此，在执行阶段，程序启动速度更快，因为其类可以从缓存中快速访问。其性能提升可以高达 42%。


### 484:	Class-File API



JDK22的JEP 457: Class-File API (Preview)提供了一个用于解析、生成和转换 Java 类文件的标准 API。
JDK23的JEP 466: Class-File API (Second Preview)则作为第二次preview。
JDK24则转为正式版本发布。

比如生成如下代码：


```java
void fooBar(boolean z, int x) {
    if (z)
        foo(x);
    else
        bar(x);
}
```


使用ASM如下：

```java
ClassWriter classWriter = ...;
MethodVisitor mv = classWriter.visitMethod(0, "fooBar", "(ZI)V", null, null);
mv.visitCode();
mv.visitVarInsn(ILOAD, 1);
Label label1 = new Label();
mv.visitJumpInsn(IFEQ, label1);
mv.visitVarInsn(ALOAD, 0);
mv.visitVarInsn(ILOAD, 2);
mv.visitMethodInsn(INVOKEVIRTUAL, "Foo", "foo", "(I)V", false);
Label label2 = new Label();
mv.visitJumpInsn(GOTO, label2);
mv.visitLabel(label1);
mv.visitVarInsn(ALOAD, 0);
mv.visitVarInsn(ILOAD, 2);
mv.visitMethodInsn(INVOKEVIRTUAL, "Foo", "bar", "(I)V", false);
mv.visitLabel(label2);
mv.visitInsn(RETURN);
mv.visitEnd();
```

使用Class-File API如下：

```java
ClassBuilder classBuilder = ...;
classBuilder.withMethod("fooBar", MethodTypeDesc.of(CD_void, CD_boolean, CD_int), flags,
                        methodBuilder -> methodBuilder.withCode(codeBuilder -> {
    Label label1 = codeBuilder.newLabel();
    Label label2 = codeBuilder.newLabel();
    codeBuilder.iload(1)
        .ifeq(label1)
        .aload(0)
        .iload(2)
        .invokevirtual(ClassDesc.of("Foo"), "foo", MethodTypeDesc.of(CD_void, CD_int))
        .goto_(label2)
        .labelBinding(label1)
        .aload(0)
        .iload(2)
        .invokevirtual(ClassDesc.of("Foo"), "bar", MethodTypeDesc.of(CD_void, CD_int))
        .labelBinding(label2);
        .return_();
});
```

### 485:	Stream Gatherers


JDK22的JEP 461: Stream Gatherers (Preview)作为第一次preview。
JDK23的JEP 473: Stream Gatherers (Second Preview)则作为第二次preview。
JDK24则作为正式版本发布。

定义示例


```java
/**
 * Gathers elements into fixed-size groups. The last group may contain fewer
 * elements.
 * @param windowSize the maximum size of the groups
 * @return a new gatherer which groups elements into fixed-size groups
 * @param <TR> the type of elements the returned gatherer consumes and produces
 */
static <TR> Gatherer<TR, ?, List<TR>> fixedWindow(int windowSize) {

    // Validate input
    if (windowSize < 1)
      throw new IllegalArgumentException("window size must be non-zero");

    // This gatherer is inherently order-dependent,
    // so it should not be parallelized
    return Gatherer.ofSequential(

            // The initializer creates an ArrayList which holds the current
            // open window
            () -> new ArrayList<TR>(windowSize),

            // The integrator is invoked for each element consumed
            Gatherer.Integrator.ofGreedy((window, element, downstream) -> {

                // Add the element to the current open window
                window.add(element);

                // Until we reach our desired window size,
                // return true to signal that more elements are desired
                if (window.size() < windowSize)
                    return true;

                // When window is full, close it by creating a copy
                var result = new ArrayList<TR>(window);

                // Clear the window so the next can be started
                window.clear();

                // Send the closed window downstream
                return downstream.push(result);

            }),

            // The combiner is omitted since this operation is intrinsically sequential,
            // and thus cannot be parallelized

            // The finisher runs when there are no more elements to pass from the upstream
            (window, downstream) -> {
                // If the downstream still accepts more elements and the current
                // open window is non-empty then send a copy of it downstream
                if(!downstream.isRejecting() && !window.isEmpty()) {
                    downstream.push(new ArrayList<TR>(window));
                    window.clear();
                }
            }

    );
}
```

使用示例

```
jshell> Stream.of(1,2,3,4,5,6,7,8,9).gather(fixedWindow(3)).toList()
$1 ==> [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
```

### 486:	Permanently Disable the Security Manager


安全性管理器（Security Manager）并不是Java客户端代码的主要安全手段，也极少用于服务器端代码。此外，维护它成本高昂。因此，在Java 17中通过JEP 411: Deprecate the Security Manager for Removal将其弃用以备移除。本特性则完全禁止开发者启用安全性管理器，Security Manager API将在未来的版本中被移除。


### 487:	Scoped Values (Fourth Preview)



JDK19的JEP 428: Structured Concurrency (Incubator)作为第一次incubator。
JDK20的JEP 437: Structured Concurrency (Second Incubator)作为第二次incubator。
JDK21的JEP 453: Structured Concurrency (Preview)作为首次preview。
JDK22的JEP 462: Structured Concurrency (Second Preview)作为第二次preview。
JDK23的JEP 480: Structured Concurrency (Third Preview)作为第三次preview。
JDK24则作为第四次preview，与JDK23不同的是callWhere以及runWhere方法从ScopedValue类中移除，可以使用ScopedValue.where()再链式调用run(Runnable)或者call(Callable)
示例：

```java
@Override
public Response handle(Request request, Response response) {
      try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
          Supplier<UserInfo>     user  = scope.fork(() -> readUserInfo());  // (1)
          Supplier<List<Offer>> offers = scope.fork(() -> fetchOffers());   // (2)
          scope.join().throwIfFailed();  // Wait for both forks
          return new Response(user.get(), order.get());
      } catch (Exception ex) {
          reportError(response, ex);
      }
}
```

### 488:	Primitive Types in Patterns, instanceof, and switch (Second Preview)


JDK19的JEP 405: Record Patterns (Preview)将Record的模式匹配作为第一次preview。
JDK20的JEP 432: Record Patterns (Second Preview)作为Record模式匹配第二次preview。
JDK21的JEP 440: Record Patterns则将Record模式匹配正式发布。
JDK23的JEP 455: Primitive Types in Patterns, instanceof, and switch (Preview)将原始类型的匹配作为第一次preview。
JDK24作为第二次preview。


```java
record Point(int x, int y) {}

// As of Java 21
static void printSum(Object obj) {
    if (obj instanceof Point(int x, int y)) {
        System.out.println(x+y);
    }
}

enum Color { RED, GREEN, BLUE }
record ColoredPoint(Point p, Color c) {}
record Rectangle(ColoredPoint upperLeft, ColoredPoint lowerRight) {}
// As of Java 21
static void printUpperLeftColoredPoint(Rectangle r) {
    if (r instanceof Rectangle(ColoredPoint ul, ColoredPoint lr)) {
         System.out.println(ul.c());
    }
}

static void printColorOfUpperLeftPoint(Rectangle r) {
    if (r instanceof Rectangle(ColoredPoint(Point p, Color c),
                               ColoredPoint lr)) {
        System.out.println(c);
    }
}
```

但是这个只是支持Record类型


在JDK14JEP 305: Pattern Matching for instanceof (Preview)作为preview。
在JDK15JEP 375: Pattern Matching for instanceof (Second Preview)作为第二轮的preview。
在JDK16JEP 394: Pattern Matching for instanceof转正。
JDK17引入JEP 406: Pattern Matching for switch (Preview)。
JDK18的JEP 420: Pattern Matching for switch (Second Preview)则作为第二轮preview。
JDK19的JEP 427: Pattern Matching for switch (Third Preview)作为第三轮preview。
JDK20的JEP 433: Pattern Matching for switch (Fourth Preview)作为第四轮preview。
JDK21的JEP 441: Pattern Matching for switch将Pattern Matching for switch作为正式版本发布，示例如下



```java
// Prior to Java 21
static String formatter(Object obj) {
    String formatted = "unknown";
    if (obj instanceof Integer i) {
        formatted = String.format("int %d", i);
    } else if (obj instanceof Long l) {
        formatted = String.format("long %d", l);
    } else if (obj instanceof Double d) {
        formatted = String.format("double %f", d);
    } else if (obj instanceof String s) {
        formatted = String.format("String %s", s);
    }
    return formatted;
}

// As of Java 21
static String formatterPatternSwitch(Object obj) {
    return switch (obj) {
        case Integer i -> String.format("int %d", i);
        case Long l    -> String.format("long %d", l);
        case Double d  -> String.format("double %f", d);
        case String s  -> String.format("String %s", s);
        default        -> obj.toString();
    };
}

// As of Java 21
static void testFooBarNew(String s) {
    switch (s) {
        case null         -> System.out.println("Oops");
        case "Foo", "Bar" -> System.out.println("Great");
        default           -> System.out.println("Ok");
    }
}

// As of Java 21
static void testStringEnhanced(String response) {
    switch (response) {
        case null -> { }
        case "y", "Y" -> {
            System.out.println("You got it");
        }
        case "n", "N" -> {
            System.out.println("Shame");
        }
        case String s
        when s.equalsIgnoreCase("YES") -> {
            System.out.println("You got it");
        }
        case String s
        when s.equalsIgnoreCase("NO") -> {
            System.out.println("Shame");
        }
        case String s -> {
            System.out.println("Sorry?");
        }
    }
}

// As of Java 21
static void exhaustiveSwitchWithBetterEnumSupport(CardClassification c) {
    switch (c) {
        case Suit.CLUBS -> {
            System.out.println("It's clubs");
        }
        case Suit.DIAMONDS -> {
            System.out.println("It's diamonds");
        }
        case Suit.HEARTS -> {
            System.out.println("It's hearts");
        }
        case Suit.SPADES -> {
            System.out.println("It's spades");
        }
        case Tarot t -> {
            System.out.println("It's a tarot");
        }
    }
}

// As of Java 21
sealed interface Currency permits Coin {}
enum Coin implements Currency { HEADS, TAILS } 

static void goodEnumSwitch1(Currency c) {
    switch (c) {
        case Coin.HEADS -> {    // Qualified name of enum constant as a label
            System.out.println("Heads");
        }
        case Coin.TAILS -> {
            System.out.println("Tails");
        }
    }
}

static void goodEnumSwitch2(Coin c) {
    switch (c) {
        case HEADS -> {
            System.out.println("Heads");
        }
        case Coin.TAILS -> {    // Unnecessary qualification but allowed
            System.out.println("Tails");
        }
    }
}

// As of Java 21
static void testNew(Object obj) {
    switch (obj) {
        case String s when s.length() == 1 -> ...
        case String s                      -> ...
        ...
    }
}
```


但是JDK21还不支持原始类型的匹配，
而支持原始类型的匹配使用示例如下：

```java
switch (x.getStatus()) {
    case 0 -> "okay";
    case 1 -> "warning";
    case 2 -> "error";
    default -> "unknown status: " + x.getStatus();
}

switch (x.getStatus()) {
    case 0 -> "okay";
    case 1 -> "warning";
    case 2 -> "error";
    case int i -> "unknown status: " + i;
}

switch (x.getYearlyFlights()) {
    case 0 -> ...;
    case 1 -> ...;
    case 2 -> issueDiscount();
    case int i when i >= 100 -> issueGoldCard();
    case int i -> ... appropriate action when i > 2 && i < 100 ...
}

long v = ...;
switch (v) {
    case 1L              -> ...;
    case 2L              -> ...;
    case 10_000_000_000L -> ...;
    case 20_000_000_000L -> ...;
    case long x          -> ... x ...;
}

if (roomSize instanceof byte) { // check if value of roomSize fits in a byte
    ... (byte) roomSize ... // yes, it fits! but cast is required
}
```

另外针对instanceof示例如下:


```java
byte b = 42;
b instanceof int;         // true (unconditionally exact)

int i = 42;
i instanceof byte;        // true (exact)

int i = 1000;
i instanceof byte;        // false (not exact)

int i = 16_777_217;       // 2^24 + 1
i instanceof float;       // false (not exact)
i instanceof double;      // true (unconditionally exact)
i instanceof Integer;     // true (unconditionally exact)
i instanceof Number;      // true (unconditionally exact)

float f = 1000.0f;
f instanceof byte;        // false
f instanceof int;         // true (exact)
f instanceof double;      // true (unconditionally exact)

double d = 1000.0d;
d instanceof byte;        // false
d instanceof int;         // true (exact)
d instanceof float;       // true (exact)

Integer ii = 1000;
ii instanceof int;        // true (exact)
ii instanceof float;      // true (exact)
ii instanceof double;     // true (exact)

Integer ii = 16_777_217;
ii instanceof float;      // false (not exact)
ii instanceof double;     // true (exact)
```


### 489:	Vector API (Ninth Incubator)

JDK16引入了JEP 338: Vector API (Incubator)提供了jdk.incubator.vector来用于矢量计算。
JDK17进行改进并作为第二轮的incubatorJEP 414: Vector API (Second Incubator)。
JDK18的JEP 417: Vector API (Third Incubator)进行改进并作为第三轮的incubator。
JDK19的JEP 426:Vector API (Fourth Incubator)作为第四轮的incubator。
JDK20的JEP 438: Vector API (Fifth Incubator)作为第五轮的incubator。
JDK21的JEP 448: Vector API (Sixth Incubator)作为第六轮的incubator。
JDK22的JEP 460: Vector API (Seventh Incubator)作为第七轮的incubator。
JDK23的JEP 469: Vector API (Eighth Incubator)作为第八轮incubator。
JDK24则作为第九轮incubator，与JDK23相比做了一些变动：比如引入了一个新的基于值的类Float16，用于表示IEEE 754二进制16格式的16位浮点数。


### 490:	ZGC: Remove the Non-Generational Mode


JDK21的JEP 439: Generational ZGC引入了分代回收。
JDK23的JEP 474: ZGC: Generational Mode by Default则将分代回收设置为默认的模式，并将非分代的模式废弃以便后续移除。
JDK24则移除了ZGC的非分代模式。


### 491:	Synchronize Virtual Threads without Pinning


JDK21引入虚拟线程时还有个pinning的问题，就是当虚拟线程在其载体上运行同步代码块时，它无法从载体上卸载。比如：


```java
class CustomerCounter {
    private final StoreRepository storeRepo;
    private int customerCount;
    CustomerCounter(StoreRepository storeRepo) {
        this.storeRepo = storeRepo;
        customerCount = 0;
    }
    synchronized void customerEnters() {
        if (customerCount < storeRepo.fetchCapacity()) {
            customerCount++;
        }
    }
    synchronized void customerExits() {
        customerCount--;
    }
}
```


如果是单纯调用storeRepo.fetchCapacity()则没问题，虚拟线程会从其载体unmount，释放平台线程给其他虚拟线程mount；但是如果是调用customerEnters，它用synchronized修饰则JVM会将该虚拟线程pin住防止其被unmount，这样子的话虚拟线程与平台线程都会blocked，直到fetchCapacity方法返回。

之所以pinning是因为synchronized依赖于monitors来确保它们只能由单个线程同时进入。在进入synchronized块之前，线程必须获取与实例相关联的monitor。JVM在平台线程级别跟踪这些monitor的所有权，而不是在虚拟线程级别跟踪。基于这些信息，假设不存在pinning，理论上，虚拟线程#1可以在synchronized块中间卸载，而虚拟线程#2可以装载到相同的平台线程上，并继续执行该synchronized块，因为承载线程是相同的，仍然持有对象的monitor。

从Java 24开始，虚拟线程可以获取、持有和释放监视器，而无需绑定到其载体线程。这意味着由于线程pinning而切换到不同的锁机制已不再是必需的。从现在起，无论是使用虚拟线程还是其他方法，性能表现都将相当一致。

在少数情况下，虚拟线程仍然会被pinning，其中一个情况是当它调用本地代码并返回到执行阻塞操作的Java代码时。在这种情况下，JDK Flight Recorder（JFR）会记录一个jdk.VirtualThreadPinned事件，如果要跟踪这些情况，可以启用JFR。


### 492:	Flexible Constructor Bodies (Third Preview)


JDK22的JEP 447: Statements before super(...) (Preview)作为第一次preview。
JDK23的JEP 482: Flexible Constructor Bodies (Second Preview)作为第二次preview。
JDK24作为第三次preview。
比如在JEP 447之前的代码如下：

```java
public class PositiveBigInteger extends BigInteger {

    public PositiveBigInteger(long value) {
        super(value);               // Potentially unnecessary work
        if (value <= 0)
            throw new IllegalArgumentException(non-positive value);
    }

}
```

在JEP 447之后代码可以如下：

```java
public class PositiveBigInteger extends BigInteger {

    public PositiveBigInteger(long value) {
        if (value <= 0)
            throw new IllegalArgumentException(non-positive value);
        super(value);
    }

}
```

### 493:	Linking Run-Time Images without JMODs



该特性能够在不使用JDK的JMOD文件的情况下创建自定义运行时映像。必须在构建JDK时启用；默认情况下不会启用它，因为一些JDK供应商可能会选择不启用它。
已安装的JDK由运行时镜像和一组以JMOD格式打包的模块组成，每个模块都在运行时镜像中。jlink在创建自定义运行时镜像时会使用JMOD文件。实际上，JDK中的运行时镜像是通过此过程生成的。这意味着JDK运行时镜像中的每个资源也存在于某个JMOD文件中，这使得已安装的JDK存在冗余问题。JMOD文件占JDK总大小的约25%。如果jlink能够改变以直接从运行时镜像中提取资源，我们可以通过简单地省略这些JMOD文件来显著减小JDK的大小。

可以使用`--enable-linkable-runtime`在构建JDK的时候使用。从Java24开始，如果jlink在模块路径上找到了JMOD文件，它将使用这些文件。只有在模块路径上未找到模块java.base时，jlink才会从运行时镜像中读取。其他模块仍然需要通过--module-path选项显式指定给jlink。

示例：

```
jlink --add-modules java.xml --output image
image/bin/java --list-modules
java.base@24
java.xml@24
```

### 494:	Module Import Declarations (Second Preview)


与JEP 477: Implicitly Declared Classes and Instance Main Methods (Third Preview)类似，本特性主要是为了简化语法方便新手使用，通过新引入module的import，来一次性导入module下所有package底下的类。
JDK23的JEP 476: Module Import Declarations (Preview)作为第一个preview。
JDK24作为第二次preview，与JDK23不同的是取消了模块不允许声明对java.base模块传递依赖的限制，并修订了java.se模块声明，使其传递性地依赖于java.base模块。这些变化意味着导入java.se模块将按需导入整个Java SE API。此外，现在允许按需声明的类型导入声明覆盖模块导入声明。

示例:

```java
import module java.base;  // 包含了import java.io.*; import java.util.*;

import module java.base;      // exports java.util, which has a public Date class
import module java.sql;       // exports java.sql, which has a public Date class

import java.sql.Date;         // resolve the ambiguity of the simple name Date!

...
Date d = ...                  // Ok!  Date is resolved to java.sql.Date
...
```


此特性需要使用`--enable-preview`参数来开启


### 495:	Simple Source Files and Instance Main Methods (Fourth Preview)


JDK21的JEP 445: Unnamed Classes and Instance Main Methods (Preview)作为首次preview，引入了未命名的类和实例main方法特性可以简化hello world示例，方便java新手入门。
JDK22的JEP 463: Implicitly Declared Classes and Instance Main Methods (Second Preview)作为第二次的preview。
JDK23的JEP 477: Implicitly Declared Classes and Instance Main Methods (Third Preview)作为第三次preview。
JDK24则作为第四次preview。



示例如下：


```java
static void main(String[] args) {
    System.out.println("static main with args");
}

static void main() {
    System.out.println("static main without args");
}

void main(String[] args) {
    System.out.println("main with args");
}

void main() {
    System.out.println("main with without args");
}
```

```
javac --release 24 --enable-preview Main.java
java --enable-preview Main
```

其中main方法选择的优先顺序是static的优于非static的，然后有args的优于没有args的


### 496:	Quantum-Resistant Module-Lattice-Based Key Encapsulation Mechanism


基于公钥的算法，如Rivest-Shamir-Adleman（RSA）和Diffie-Hellman，越来越多地面临实际量子计算攻击的风险，但它们仍然被Java平台使用（用于诸如对JAR文件进行数字签名或通过TLS建立安全网络连接等任务）。为了解决这个问题，人们发明了抗量子密码学算法，这个JEP引入了其中一种算法的实现：Module-Lattice-Based Key-Encapsulation Mechanism (ML-KEM)。

生成ML-KEM key pair示例：


```java
KeyPairGenerator generator = KeyPairGenerator.getInstance("ML-KEM");
generator.initialize(NamedParameterSpec.ML_KEM_512);
KeyPair keyPair = generator.generateKeyPair(); // an ML-KEM-512 key pair
KeyPairGenerator generator = KeyPairGenerator.getInstance("ML-KEM");
KeyPair keyPair = generator.generateKeyPair(); // an ML-KEM-768 key pair by default
KeyPairGenerator generator = KeyPairGenerator.getInstance("ML-KEM-1024");
KeyPair keyPair = generator.generateKeyPair(); // an ML-KEM-1024 key pair
```


使用示例：

```java
KEM ks = KEM.getInstance("ML-KEM");
KEM.Encapsulator enc = ks.newEncapsulator(publicKey);
KEM.Encapsulated encap = enc.encapsulate();
byte[] msg = encap.encapsulation();     // send this to receiver
SecretKey sks = encap.key();

byte[] msg = ...;                       // received from sender
KEM kr = KEM.getInstance("ML-KEM");
KEM.Decapsulator dec = kr.newDecapsulator(privateKey);
SecretKey skr = dec.decapsulate(msg);
```

### 497:	Quantum-Resistant Module-Lattice-Based Digital Signature Algorithm


与JEP496类，本JEP引入的是另外一种quantum-resistant算法：Module-Lattice-Based Digital Signature Algorithm (ML-DSA)。
生成ML-DSA Key Pairs示例：


```java
KeyPairGenerator generator = KeyPairGenerator.getInstance("ML-DSA");
generator.initialize(NamedParameterSpec.ML_DSA_44);
KeyPair keyPair = generator.generateKeyPair(); // an ML-DSA-44 key pair
KeyPairGenerator generator = KeyPairGenerator.getInstance("ML-DSA");
KeyPair keyPair = generator.generateKeyPair(); // an ML-DSA-65 key pair by default
KeyPairGenerator generator = KeyPairGenerator.getInstance("ML-DSA-87");
KeyPair keyPair = generator.generateKeyPair(); // an ML-DSA-87 key pair
```


使用示例：


```java
byte[] msg = ...;
Signature ss = Signature.getInstance("ML-DSA");
ss.initSign(privateKey);
ss.update(msg);
byte[] sig = ss.sign();

byte[] msg = ...;
byte[] sig = ...;
Signature sv = Signature.getInstance("ML-DSA");
sv.initVerify(publicKey);
sv.update(msg);
boolean verified = sv.verify(sig);
```

### 498:	Warn upon Use of Memory-Access Methods in sun.misc.Unsafe



JDK9的JEP 193: Variable Handles引入了VarHandle API用于替代sun.misc.Unsafe。
JDK14的JEP 370: Foreign-Memory Access API (Incubator)引入了Foreign-Memory Access API作为incubator。
JDK15的JEP 383: Foreign-Memory Access API (Second Incubator)Foreign-Memory Access API作为第二轮incubator。
JDK16的JEP 393: Foreign-Memory Access API (Third Incubator)作为第三轮，它引入了Foreign Linker API (JEP 389)。
FFM API在JDK 17的JEP 412: Foreign Function & Memory API (Incubator)作为incubator引入。
FFM API在JDK 18的JEP 419: Foreign Function & Memory API (Second Incubator)作为第二轮incubator。
JDK19的JEP 424: Foreign Function & Memory API (Preview)则将FFM API作为preview API。
JDK20的JEP 434: Foreign Function & Memory API (Second Preview)作为第二轮preview。
JDK21的JEP 442: Foreign Function & Memory API (Third Preview)作为第三轮preview。
JDK22的JEP 454: Foreign Function & Memory API则正式发布此特性。
JDK23的JEP 471: Deprecate the Memory-Access Methods in sun.misc.Unsafe for Removal废弃sun.misc.Unsafe，以便后续版本移除。
JDK24默认情况下将在首次使用任何内存访问方法时发出警告，无论这些方法是直接调用还是通过反射调用。也就是说，无论使用了哪些内存访问方法，以及任何特定方法被调用的次数如何，最多只会发出一次警告。这将提醒应用程序开发者和用户即将移除这些方法，并需要升级库。警告示例如下：


```
WARNING: A terminally deprecated method in sun.misc.Unsafe has been called
WARNING: sun.misc.Unsafe::setMemory has been called by com.foo.bar.Server (file:/tmp/foobarserver/thing.jar)
WARNING: Please consider reporting this to the maintainers of com.foo.bar.Server
WARNING: sun.misc.Unsafe::setMemory will be removed in a future release
```

### 499:	Structured Concurrency (Fourth Preview)


JDK19的JEP 428: Structured Concurrency (Incubator)作为第一次incubator。
JDK20的JEP 437: Structured Concurrency (Second Incubator)作为第二次incubator。
JDK21的JEP 453: Structured Concurrency (Preview)作为首次preview。
JDK22的JEP 462: Structured Concurrency (Second Preview)作为第二次preview。
JDK23的JEP 480: Structured Concurrency (Third Preview)作为第三次preview。
JDK24作为第四次preview。


使用示例如下:


```java
Response handle() throws ExecutionException, InterruptedException {
    try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
        Supplier<String>  user  = scope.fork(() -> findUser());
        Supplier<Integer> order = scope.fork(() -> fetchOrder());

        scope.join()            // Join both subtasks
             .throwIfFailed();  // ... and propagate errors

        // Here, both subtasks have succeeded, so compose their results
        return new Response(user.get(), order.get());
    }
}
```

### 501:	Deprecate the 32-bit x86 Port for Removal



支持多个平台一直是Java生态系统从一开始就关注的重点。然而，较旧的平台无法无限期地支持，这也是为什么32位x86（Linux）现在计划被移除的原因之一。维护它所需的精力超过了其优势。为了与新特性（如Loom、外函数与内存API（FFM）、向量API以及晚期GC屏障扩展）保持同步，维护成本显著增加，因此它现在已被弃用。

在JDK 24中，配置32位x86构建将失败。可以通过使用新的构建配置选项`--enable-deprecated-ports=yes`来抑制此错误。这意味着32位用户仍然可以使用JDK 24；然而，在未来的版本中，支持将被完全移除，届时受影响的用户预计已经迁移到64位JVM。


## 参考引用

* 本文同步至:  <https://waylau.com/jdk-24-released/>
* <https://waylau.com/jdk-23-released/>
* <https://waylau.com/jdk-22-released/>
* <https://waylau.com/jdk-21-released/>
* <https://waylau.com/jdk-17-released/>
* <https://waylau.com/jdk-16-released/>
* <https://waylau.com/jdk-15-released/>
* <https://waylau.com/jdk-14-released/>
* 《[Java核心编程](https://item.jd.com/12868796.html)》
* 开源项目“现代Java案例大全” <https://github.com/waylau/modern-java-demos>