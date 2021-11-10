---
layout: post
title: JUnit 4断言void方法
date: 2021-10-26 00:22
author: admin
comments: true
categories: [JUnit]
tags: [JUnit]
---

假设service.runTask是void方法，没有返回值。也不期望抛出异常，可以按下面的方法玩。

```java
boolean result;
try {
    service.runTask(engineBatchVO);
    result = true;
} catch (ApplicationException ex) {
    result = false;
}

Assert.assertTrue(result);
```