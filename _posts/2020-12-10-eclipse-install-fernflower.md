---
layout: post
title: Eclipse中安装反编译工具Fernflower（Enhanced Class Decompiler）
date: 2020-12-10 00:22
author: admin
comments: true
categories: [Eclipse,Fernflower]
tags: [Eclipse,Fernflower]
---

IntelliJ IDEA一个吸引人的地方在于，他有比较好的反编译工具，这让Eclipse用户牙痒痒。但不要紧，本文介绍如何在Eclipse IDE中使用IntelliJ IDEA的反编译工具Fernflower。


<!-- more -->



## 为啥需要反编译
 

很多jar不提供源码，那么打开class是这个鸟样。

 
![](https://communityfile-drcn.op.hicloud.com/FileServer/getFile/cmtybbs/042/413/002/0000000000042413002.20201209172728.68618079644815234365169366505005:50511210150037:2800:F82626AC18F21C878853870CFC50BFFA00B773240BC32CC786A2D7FCFBCB8657.png)

 

不具备人类可读性。因此需要反编译。

## 什么是Fernflower

那么我是怎么知道Fernflower的呢？你随便用IntelliJ IDEA打开一个jar中的class文件，可以看到下面的信息，这就是IEDA中的反编译工具Fernflower。

![](https://communityfile-drcn.op.hicloud.com/FileServer/getFile/cmtybbs/042/413/002/0000000000042413002.20201209172737.11522191552170031663226548744527:50511210150037:2800:81EE3FECC4223B8871036EDB4ADB0808C56967E348C181FA62B9C5B7452DF40B.png)

 

看官网介绍Fernflower（<https://github.com/JetBrains/intellij-community/tree/master/plugins/java-decompiler/engine>）

```
Fernflower is the first actually working analytical decompiler for Java and probably for a high-level programming language in general


“Fernflower是第一个真正为Java工作分析反编译器，通常也适用于一般的高级编程语言”
```
 

看介绍是很牛批的样子，当然实际也是。

 

## 如何获取Fernflower
 

非常遗憾的是，Fernflower是IntelliJ IDEA独家所有，那我是怎么搞定的呢？

我先在eclipse市场找了下，没有找到Fernflower，却找到了Enhanced Class Decompiler


![](https://communityfile-drcn.op.hicloud.com/FileServer/getFile/cmtybbs/042/413/002/0000000000042413002.20201209172743.72650839274711595789247478013411:50511210150037:2800:2A269E6849CC0C2B3A3ECEF9C4E91B177BA40BBFEDE46F000C4D76A8DC6EB248.png)

 
 

看官网介绍（<https://marketplace.eclipse.org/content/enhanced-class-decompiler>）

 

 
```
Enhanced Class Decompiler integrates JD, Jad, FernFlower, CFR, Procyon seamlessly with Eclipse and allows Java developers to debug class files without source code directly. It also integrates with the eclipse class editor, m2e plugin, supports Javadoc, reference search, library source attaching, byte code view and the syntax of JDK8 lambda expression.
```
 

简言之，Enhanced Class Decompiler集JD、Jad、FernFlower、CFR、Procyon等各种反编译工具之大成。换言之，FernFlower就是我Enhanced Class Decompiler的一个子集呗。

 

呵呵，好一招曲线救国。用Enhanced Class Decompiler变相用了FernFlower。

 

## 如何在Eclipse IDE中安装Fernflower
 

### 1. 在线安装

这是最简单的方式。使用Eclipse的同学都懂。

 

安装地址是： <https://ecd-plugin.github.io/update>

 
![](https://communityfile-drcn.op.hicloud.com/FileServer/getFile/cmtybbs/042/413/002/0000000000042413002.20201209172751.72799032971404894003184429355435:50511210150037:2800:A84ABABB1CF7FC9626CE90CFA8FD684D3AB8902AE59EC66D890740410C6E8E88.png)

 
 


### 2. 离线安装
 

获取离线安装包zip文件（见附件），在“Add Repository”中指定该zip文件即可。

 
 
![](https://communityfile-drcn.op.hicloud.com/FileServer/getFile/cmtybbs/042/413/002/0000000000042413002.20201209172757.63496189578699403736726936539908:50511210150037:2800:247DDCD87901CCBD801721B231237B0F28B1115A67D4588412843D2D183AA0EF.png)

 
 



 

### 3. 可选组件

一般就选Core就够用了，不嫌多就全选上。


 
![](https://communityfile-drcn.op.hicloud.com/FileServer/getFile/cmtybbs/042/413/002/0000000000042413002.20201209172803.81106014001862284984179912834617:50511210150037:2800:ECADD32D91129A5450B06B59F256D9B078A5EEBCC1A28EDC352B0EF3B6CFB195.png)

 
 

装完重启Eclipse就能看到这个工具了。

![](https://communityfile-drcn.op.hicloud.com/FileServer/getFile/cmtybbs/042/413/002/0000000000042413002.20201209172809.44778538184865823654168189368455:50511210150037:2800:045EF2C279656AE7F245E03A68EDE44876FF5A6D37A118CE76652F02D949B990.png)

 
 



## 怎么使用Fernflower

右键class文件，使用如何在Eclipse IDE中安装FernFlower打开即可

 

 
![](https://communityfile-drcn.op.hicloud.com/FileServer/getFile/cmtybbs/042/413/002/0000000000042413002.20201209172816.08274671708397799385294949267303:50511210150037:2800:D929D63D457AA4B0DB34A1551731D2A76EE6540420B984B56B547969045CF4E9.png)

 
 


 

反编译成功！看到庐山真面目了。



 

 
![](https://communityfile-drcn.op.hicloud.com/FileServer/getFile/cmtybbs/042/413/002/0000000000042413002.20201209172823.65691650052685539280873763203272:50511210150037:2800:6B9FA56BEFB21CD33AE0F119F863335ED739409BFE8D61F4E4897D5B9871C9EF.png)

 


## 参考引用

* 原文同步至 <https://waylau.com/eclipse-install-fernflower/>
