---
layout: post
title: Java三元表达式中的陷阱 
date: 2020-11-11 00:22
author: admin
comments: true
categories: [Java]
tags: [Java]
---

今天遇到一个奇怪的Java三元表达式中的空指针异常。特此记录。

<!-- more -->



## 代码

代码示意如下：

```java
Integer itemVO = null;
Integer globleLatenessToleranceUseAlternate = null;
Integer latenessToleranceUseAlternate =
    (itemVO == null ? globleLatenessToleranceUseAlternate : itemVO.intValue());
```

从上面代码可以看出：当itemVO不为空时，就取itemVO的值；否则，就取globleLatenessToleranceUseAlternate的值。

## 原因

但问题就在globleLatenessToleranceUseAlternate。当itemVO为空时，如果取globleLatenessToleranceUseAlternate，并不会得到值null，
而是Java会把globleLatenessToleranceUseAlternate进行一个自动开箱拆箱处理。简言之，取得是 globleLatenessToleranceUseAlternate.intValue()，
此时，因为globleLatenessToleranceUseAlternate 本身是 null，因此 globleLatenessToleranceUseAlternate.intValue() 导致了空指针因此。

## 解法

修改如下解决：

```java
Integer itemVO = null;
Integer globleLatenessToleranceUseAlternate = null;
Integer latenessToleranceUseAlternate;

if (itemVO != null) {
    latenessToleranceUseAlternate = itemVO.intValue();
} else {
    latenessToleranceUseAlternate = globleLatenessToleranceUseAlternate;
}

```



值得注意的是，在新版的JDK和Eclipse中，会做出友好的提示，从而能够有效规避上述问题。提示如下：


```
Null pointer access: This expression of type Integer is null but requires auto-unboxing
```


## 参考引用

* * [Java核心编程](https://github.com/waylau/modern-java-demos)