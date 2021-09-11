---
layout: post
title: Java将HashMap的值转为List
date: 2021-09-09 00:22
author: admin
comments: true
categories: [Java]
tags: [Java]
---

Java如何来将HashMap的值转为List呢？且看本文。

<!-- more -->

## 问题

代码如下：

```java
private final Map<String, RuleParam> quantityMap = new ConcurrentHashMap<String, RuleParam>(16);

if (!quantityMap.isEmpty()) {
    List<RuleParam> ruleParamList = (List<RuleParam>) quantityMap.values();
}
```

上述代码，会报异常“java.util.concurrent.ConcurrentHashMap$ValuesView cannot be cast to java.util.List”。

## 原因

因为`HashMap#values()` 返回`java.util.Collection<V>`，并不能将强制转换到ArrayList中，因获得ClassCastException。

## 解决

建议使用`ArrayList(Collection<? extends V>)`构造函数。此构造函数接受一个实现了`Collection<? extends V>`接口的参数。


```java
private final Map<String, RuleParam> quantityMap = new ConcurrentHashMap<String, RuleParam>(16);

if (!quantityMap.isEmpty()) {
    List<RuleParam> ruleParamList = new ArrayList<>(quantityMap.values());
}
```


## 参考

https://stackoverflow.com/questions/15522546/why-hashmap-values-are-not-cast-in-list