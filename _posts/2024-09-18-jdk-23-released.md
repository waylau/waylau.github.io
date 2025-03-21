---
layout: post
title: JDK 23发布
date: 2024-09-18 00:22
author: admin
comments: true
categories: [JDK,Java]
tags: [JDK,Java]
---

JDK 23已经于2024年9月17日正式发布。本文总结了JDK 23发布的新特性。

<!-- more -->



## 安装包下载

主要分为OpenJDK版本和Oracle版本，下载地址如下：

* OpenJDK版本：<https://jdk.java.net/23/>
* Oracle版本：<https://www.oracle.com/java/technologies/downloads/>


上述版本，如果是个人学习用途，则差异不大。但如果是用于商业用途，则需要仔细看好相关的授权。Oracle JDK商用需要遵循 [Oracle No-Fee Terms and Conditions (NFTC)](https://www.oracle.com/downloads/licenses/javase-license1.html)协议，而OpenJDK根据GPL v2许可获得许可。 


更多有关Java的基本知识，可以参阅《[Java核心编程](https://item.jd.com/12868796.html)》这本书，描述的非常详细。


## JDK 23 新特性说明

### 455: Primitive Types in Patterns, instanceof, and switch (Preview)



JDK19的JEP 405: Record Patterns (Preview)将Record的模式匹配作为第一次preview。
JDK20的JEP 432: Record Patterns (Second Preview)作为Record模式匹配第二次preview。
JDK21的JEP 440: Record Patterns则将Record模式匹配正式发布，使用示例如下：


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

* 在JDK14JEP 305: Pattern Matching for instanceof (Preview)作为preview
* 在JDK15JEP 375: Pattern Matching for instanceof (Second Preview)作为第二轮的preview
* 在JDK16JEP 394: Pattern Matching for instanceof转正
* JDK17引入JEP 406: Pattern Matching for switch (Preview)
* JDK18的JEP 420: Pattern Matching for switch (Second Preview)则作为第二轮preview
* JDK19的JEP 427: Pattern Matching for switch (Third Preview)作为第三轮preview
* JDK20的JEP 433: Pattern Matching for switch (Fourth Preview)作为第四轮preview
* JDK21的JEP 441: Pattern Matching for switch将Pattern Matching for switch作为正式版本发布，示例如下

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

但是还不支持原始类型的匹配，而此次的功能则是将原始类型的匹配作为第一轮的preview，使用示例如下：


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



### 466: Class-File API (Second Preview)


jdk22的JEP 457: Class-File API (Preview)提供了一个用于解析、生成和转换 Java 类文件的标准 API。
jdk23则作为第二次preview，比如要生成如下类。

```java
void fooBar(boolean z, int x) {
    if (z)
        foo(x);
    else
        bar(x);
}
```

使用ASM：


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

而使用Class-File API则如下:

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

### 467: Markdown Documentation Comments



java doc的注释目前已经支持html语法，本特性则支持使用markdown语法，现有的使用html语法的示例如下:

```java
/**
 * Returns a hash code value for the object. This method is
 * supported for the benefit of hash tables such as those provided by
 * {@link java.util.HashMap}.
 * <p>
 * The general contract of {@code hashCode} is:
 * <ul>
 * <li>Whenever it is invoked on the same object more than once during
 *     an execution of a Java application, the {@code hashCode} method
 *     must consistently return the same integer, provided no information
 *     used in {@code equals} comparisons on the object is modified.
 *     This integer need not remain consistent from one execution of an
 *     application to another execution of the same application.
 * <li>If two objects are equal according to the {@link
 *     #equals(Object) equals} method, then calling the {@code
 *     hashCode} method on each of the two objects must produce the
 *     same integer result.
 * <li>It is <em>not</em> required that if two objects are unequal
 *     according to the {@link #equals(Object) equals} method, then
 *     calling the {@code hashCode} method on each of the two objects
 *     must produce distinct integer results.  However, the programmer
 *     should be aware that producing distinct integer results for
 *     unequal objects may improve the performance of hash tables.
 * </ul>
 *
 * @implSpec
 * As far as is reasonably practical, the {@code hashCode} method defined
 * by class {@code Object} returns distinct integers for distinct objects.
 *
 * @return  a hash code value for this object.
 * @see     java.lang.Object#equals(java.lang.Object)
 * @see     java.lang.System#identityHashCode
 */
```

使用markdown示例如下:


```java
/// Returns a hash code value for the object. This method is
/// supported for the benefit of hash tables such as those provided by
/// [java.util.HashMap].
///
/// The general contract of `hashCode` is:
///
///   - Whenever it is invoked on the same object more than once during
///     an execution of a Java application, the `hashCode` method
///     must consistently return the same integer, provided no information
///     used in `equals` comparisons on the object is modified.
///     This integer need not remain consistent from one execution of an
///     application to another execution of the same application.
///   - If two objects are equal according to the
///     [equals][#equals(Object)] method, then calling the
///     `hashCode` method on each of the two objects must produce the
///     same integer result.
///   - It is _not_ required that if two objects are unequal
///     according to the [equals][#equals(Object)] method, then
///     calling the `hashCode` method on each of the two objects
///     must produce distinct integer results.  However, the programmer
///     should be aware that producing distinct integer results for
///     unequal objects may improve the performance of hash tables.
///
/// @implSpec
/// As far as is reasonably practical, the `hashCode` method defined
/// by class `Object` returns distinct integers for distinct objects.
///
/// @return  a hash code value for this object.
/// @see     java.lang.Object#equals(java.lang.Object)
/// @see     java.lang.System#identityHashCode
```

需要以`///`开头来表示使用markdown的语法。


### 469: Vector API (Eighth Incubator)


JDK16引入了JEP 338: Vector API (Incubator)提供了jdk.incubator.vector来用于矢量计算。
JDK17进行改进并作为第二轮的incubatorJEP 414: Vector API (Second Incubator)。
JDK18的JEP 417: Vector API (Third Incubator)进行改进并作为第三轮的incubator。
JDK19的JEP 426:Vector API (Fourth Incubator)作为第四轮的incubator。
JDK20的JEP 438: Vector API (Fifth Incubator)作为第五轮的incubator。
JDK21的JEP 448: Vector API (Sixth Incubator)作为第六轮的incubator。
JDK22的JEP 460: Vector API (Seventh Incubator)作为第七轮的incubator。
JDK23则作为第八轮incubator。

使用示例如下


```java
void scalarComputation(float[] a, float[] b, float[] c) {
   for (int i = 0; i < a.length; i++) {
        c[i] = (a[i] * a[i] + b[i] * b[i]) * -1.0f;
   }
}

static final VectorSpecies<Float> SPECIES = FloatVector.SPECIES_PREFERRED;

void vectorComputation(float[] a, float[] b, float[] c) {
    int i = 0;
    int upperBound = SPECIES.loopBound(a.length);
    for (; i < upperBound; i += SPECIES.length()) {
        // FloatVector va, vb, vc;
        var va = FloatVector.fromArray(SPECIES, a, i);
        var vb = FloatVector.fromArray(SPECIES, b, i);
        var vc = va.mul(va)
                   .add(vb.mul(vb))
                   .neg();
        vc.intoArray(c, i);
    }
    for (; i < a.length; i++) {
        c[i] = (a[i] * a[i] + b[i] * b[i]) * -1.0f;
    }
}
```

### 473: Stream Gatherers (Second Preview)


JDK22的JEP 461: Stream Gatherers (Preview)作为第一次preview。
JDK23则作为第二次preview，定义示例:


```java
static <TR> Gatherer<TR, ?, TR> selectOne(BinaryOperator<TR> selector) {

    // Validate input
    Objects.requireNonNull(selector, "selector must not be null");

    // Private state to track information across elements
    class State {
        TR value;            // The current best value
        boolean hasValue;    // true when value holds a valid value
    }

    // Use the `of` factory method to construct a gatherer given a set
    // of functions for `initializer`, `integrator`, `combiner`, and `finisher`
    return Gatherer.of(

            // The initializer creates a new State instance
            State::new,

            // The integrator; in this case we use `ofGreedy` to signal
            // that this integerator will never short-circuit
            Gatherer.Integrator.ofGreedy((state, element, downstream) -> {
                if (!state.hasValue) {
                    // The first element, just save it
                    state.value = element;
                    state.hasValue = true;
                } else {
                    // Select which value of the two to save, and save it
                    state.value = selector.apply(state.value, element);
                }
                return true;
            }),

            // The combiner, used during parallel evaluation
            (leftState, rightState) -> {
                if (!leftState.hasValue) {
                    // If no value on the left, return the right
                    return rightState;
                } else if (!rightState.hasValue) {
                    // If no value on the right, return the left
                    return leftState;
                } else {
                    // If both sides have values, select one of them to keep
                    // and store it in the leftState, as that will be returned
                    leftState.value = selector.apply(leftState.value,
                                                     rightState.value);
                    return leftState;
                }
            },

            // The finisher
            (state, downstream) -> {
                // Emit the selected value, if there is one, downstream
                if (state.hasValue)
                    downstream.push(state.value);
            }

    );
}
```

使用示例:

```
jshell> Stream.generate(() -> ThreadLocalRandom.current().nextInt())
              .limit(1000)                   // Take the first 1000 elements
              .gather(selectOne(Math::max))  // Select the largest value seen
              .parallel()                    // Execute in parallel
              .findFirst()                   // Extract the largest value
$1 ==> Optional[99822]
```

### 471: Deprecate the Memory-Access Methods in sun.misc.Unsafe for Removal


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

JDK23的本特性则是废弃sun.misc.Unsafe，以便后续版本移除。


### 474: ZGC: Generational Mode by Default


JDK21的JEP 439: Generational ZGC引入了分代回收。
JDK23则将分代回收设置为默认的模式，并将非分代的模式废弃以便后续移除。

以前需要`-XX:+UseZGC -XX:+ZGenerational`开启分代，JDK23及以后版本就不需要显示使用`-XX:+ZGenerational`参数了。


### 476: Module Import Declarations (Preview)


与JEP 477: Implicitly Declared Classes and Instance Main Methods (Third Preview)类似，本特性主要是为了简化语法方便新手使用，通过新引入module的import，来一次性导入module下所有package底下的类，示例:


```java
import module java.base;  // 包含了import java.io.*; import java.util.*;

import module java.base;      // exports java.util, which has a public Date class
import module java.sql;       // exports java.sql, which has a public Date class

import java.sql.Date;         // resolve the ambiguity of the simple name Date!

...
Date d = ...                  // Ok!  Date is resolved to java.sql.Date
...
```


此特性需要使用`--enable-preview`参数来开启。


### 477: Implicitly Declared Classes and Instance Main Methods (Third Preview)


JDK21的JEP 445: Unnamed Classes and Instance Main Methods (Preview)作为首次preview，引入了未命名的类和实例main方法特性可以简化hello world示例，方便java新手入门。
JDK22的JEP 463: Implicitly Declared Classes and Instance Main Methods (Second Preview)作为第二次的preview。
JDK23则作为第三次preview，示例如下


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
javac --release 23 --enable-preview Main.java
java --enable-preview Main
```


其中main方法选择的优先顺序是static的优于非static的，然后有args的优于没有args的。


### 480: Structured Concurrency (Third Preview)


JDK19的JEP 428: Structured Concurrency (Incubator)作为第一次incubator。
JDK20的JEP 437: Structured Concurrency (Second Incubator)作为第二次incubator。
JDK21的JEP 453: Structured Concurrency (Preview)作为首次preview。
JDK22的JEP 462: Structured Concurrency (Second Preview)作为第二次preview。
JDK23则作为第三次preview，使用示例如下:

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

### 481: Scoped Values (Third Preview)


JDK20的JEP 429: Scoped Values (Incubator)作为Incubator。
JDK21的JEP 446: Scoped Values (Preview)作为preview版本。
JDK22的JEP 464: Scoped Values (Second Preview)作为第二次preview。
JDK23则作为第三次preview，使用示例如下:


```java
class Framework {

    private final static ScopedValue<FrameworkContext> CONTEXT
                        = ScopedValue.newInstance();    // (1)声明

    void serve(Request request, Response response) {
        var context = createContext(request);
        ScopedValue.runWhere(CONTEXT, context,          // (2)设置
                   () -> Application.handle(request, response));
    }
    
    public PersistedObject readKey(String key) {
        var context = CONTEXT.get();                    // (3)读取
        var db = getDBConnection(context);
        db.readKey(key);
    }

}
```

### 482: Flexible Constructor Bodies (Second Preview)



JDK22的JEP 447: Statements before super(…) (Preview)作为第一次preview。JDK23则作为第二次preview。

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

## 参考引用

* 本文同步至:  <https://waylau.com/jdk-23-released/>
* <https://waylau.com/jdk-22-released/>
* <https://waylau.com/jdk-21-released/>
* <https://waylau.com/jdk-17-released/>
* <https://waylau.com/jdk-16-released/>
* <https://waylau.com/jdk-15-released/>
* <https://waylau.com/jdk-14-released/>
* 《[Java核心编程](https://item.jd.com/12868796.html)》
* 开源项目“现代Java案例大全” <https://github.com/waylau/modern-java-demos>