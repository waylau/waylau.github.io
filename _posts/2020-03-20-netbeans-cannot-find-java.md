---
layout: post
title: NetBeans启动报“Cannot find Java 1.8 or higher”错误
date: 2020-03-20 00:22
author: admin
comments: true
categories: [NetBeans,Java]
tags: [NetBeans,Java]
---

初次使用NetBeans，往往会遇到“Cannot find Java”错误。本文介绍如何解决该错误。

<!-- more -->



## 下载

在<https://netbeans.apache.org/download/>页面可以看到最新的Apache NetBeans的下载地址。

以Apache NetBeans 11.3为例，下载地址为<https://netbeans.apache.org/download/nb113/nb113.html>

选择下载netbeans-11.3-bin.zip 

## 运行

解压zip文件到任意目录。


双击bin目录下的.exe文件就能运行NetBeans。如果是64位的系统，就选netbeans64.exe。

很不幸，你可能会遇到以下错误“Cannot find Java 1.8 or higher”。

## 解决

解决的方式是，在etc目录下的netbeans.conf配置文件里面配置JDK路径。示例如下

```
netbeans_jdkhome="D:\Program Files\jdk-14"
```

## 参考引用

* 本文同步至: <https://waylau.com/netbeans-cannot-find-java/>
