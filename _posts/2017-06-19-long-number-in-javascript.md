---
layout: post
title: JavaScript 中的长整型精度问题
date: 2017-06-19 00:22
author: admin
comments: true
categories: [JavaScript]
tags: [JavaScript]
---

JavaScript 在处理长整型时会有一定的问题，有时精度会缺失。

<!-- more -->

## 问题

当 Java 后台有个 Long 型值`20175678901234567`转到前台时，发现精度存在问题:

```javascript
var batchNumber = 20175678901234567;  
console.log(batchNumber);  // 20175678901234570
```

也就是说，到了前端 JavaScript 接收后，值变成了`20175678901234570`。


## 原理

JavaScript 中所有的数字类型，实际存储都是通过 8 字节 double 浮点型 表示的。浮点数并不是能够精确表示范围内的所有数的， 虽然 double 浮点型的范围看上去很大: 2.23x10^(-308) ~ 1.79x10^308。 可以表示的最大整数可以很大，但能够精确表示，使用算数运算的并没有这么大。

比如，简单的加法运算，结果也是出人意料：

```javascript
var x = 0.2;  
var y = 0.1;  
console.log(x+y);  // 0.30000000000000004
```

JavaScript 有所谓的最大和最小安全值：

```javascript
console.log(Number.MAX_SAFE_INTEGER); //9007199254740991
console.log(Number.MIN_SAFE_INTEGER); //-9007199254740991
```

在安全值范围内的整形都能够正常显示，超出安全值范围的整型则结果都是错误的结果， 因为它们进行的都是浮点数运算会丢失精度，都不可靠。

```javascript
console.log(Number.MAX_SAFE_INTEGER + 2);  // 9007199254740992
console.log(Number.MAX_SAFE_INTEGER + 2000);  // 9007199254742992
```

## 解决方案

直接将JavaScript 中的长整型以字符串形式来表示就不会有精度问题:


```javascript
var batchNumberStr = '20175678901234567';  
console.log(batchNumberStr);  // 20175678901234567
```


对于 Node.js 来说，也有一些开源库 [bignum](https://github.com/justmoon/node-bignum)、[bigint](https://github.com/substack/node-bigint)的等对于长整型的支持。
