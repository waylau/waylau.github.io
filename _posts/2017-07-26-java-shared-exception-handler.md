---
layout: post
title: Java 多个异常共享同一个异常处理器
date: 2017-07-26 00:22
author: admin
comments: true
categories: [Java]
tags: [Java,exception]
---

Java 的异常处理机制，在 Java 7 中有了非常大的改进。其中一个特性就是，支持多个异常共享同一个异常处理器。

<!-- more -->

## 传统的异常处理

我们先来看下，传统的异常处理方式：

```java
// not share exception handler
int[] intArray = new int[3];

try {
	for (int i = 0; i <= intArray.length; i++) {
		intArray[i] = i;
		System.out.println("intArray[" + i + "] = " + intArray[i]);
		System.out.println("intArray[" + i + "]模 " + (i - 2) + "的值:  " + intArray[i] % (i - 2));
	}
} catch (ArrayIndexOutOfBoundsException e) {

	// ...省略了大段处理逻辑
	System.out.println("异常信息：" + e);
} catch (ArithmeticException e) {

	// ...省略了大段处理逻辑
	System.out.println("异常信息：" + e);
}
```

在上面的程序处理中，我们捕获了可能出现的 ArrayIndexOutOfBoundsException 和 ArithmeticException 的异常。

## 共享同一个异常处理器

为了改进代码，我们使用了 Java 7 中共享同一个异常处理器的方式，代码改成了如下方式：

```java
// share exception handler
intArray = new int[3];

try {
	for (int i = 0; i <= intArray.length; i++) {
		intArray[i] = i;
		System.out.println("intArray[" + i + "] = " + intArray[i]);
		System.out.println("intArray[" + i + "]模 " + (i - 2) + "的值:  " + intArray[i] % (i - 2));
	}
} catch (ArrayIndexOutOfBoundsException | ArithmeticException e) {

	// ...省略了大段处理逻辑
	System.out.println("异常信息：" + e);
}  
System.out.println("程序正常结束。");
```

在这段程序中，我们看到  ArrayIndexOutOfBoundsException 和 ArithmeticException 异常被放到了同一个异常处理器中进行了处理。这样整体代码看起来更加简洁，并消除了很多的重复代码。要知道，重复是魔鬼。


## 源码

本例子的源码，可以在 <https://github.com/waylau/essential-java> 中 `com.waylau.essentialjava.exception` 包下找到。

## 参考文献

* 《Java 编程要点》（<https://github.com/waylau/essential-java>）



