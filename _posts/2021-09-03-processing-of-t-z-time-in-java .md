---
layout: post
title: Java开发涉及时间里面的TZ的处理
date: 2021-09-03 00:22
author: admin
comments: true
categories: [Java]
tags: [Java]
---

在编程语言中，时间往往是一个相比比较复杂的类型。从狭义上讲，时间又可以细分为日期、日历、时间戳、时区等众多的概念。

<!-- more -->

有关日期、日历、时间戳、时区等众多的概念。可以看这篇介绍（<https://developer.huawei.com/consumer/cn/forum/topic/0202665874275260083?fid=23>）。

假设有这么一个时间“2016-08-9T10:01:54.123Z”里面又有Z又有T，啥意思呢？含义如下：

* 字母T：表示后面跟的时间
* 最末尾的Z：表示UTC统一时间
* 123：表示的毫秒

我们看一个如何获得北京时间的例子，看如下代码：

```java
String str = "2016-08-09T10:01:54.123Z";

String utcStr = str.replace("Z", " UTC"); // 转为空格+UTC

SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS Z");

Date date = sdf.parse(utcStr); // 格式化

System.out.println(date); // 输出Tue Aug 09 18:01:54 CST 2016
```

这样我们就获得了一个本地的时间（即北京时间）：`Tue Aug 09 18:01:54 CST 2016`

北京时间等同于CST（中国标准时间）。CST是UTC+8时区的知名名称之一，比UTC（协调世界时)提前8个小时,与UTC的时间偏差可写为+08:00. 它被用作标准时间。