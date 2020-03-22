---
layout: post
title: Eclipse IDE支持Java 14
date: 2020-03-23 00:22
author: admin
comments: true
categories: [Eclipse,Java]
tags: [Eclipse,Java]
---

随着JDK 14的发布（<https://waylau.com/jdk-14-released/>），各大Java IDE也开始支持JDK 14。最新版本的Eclipse IDE 2020-03也于2020年3月18日发布，本文介绍如何通过Eclipse IDE来开发Java 14应用。

<!-- more -->


## 下载

下载地址<https://www.eclipse.org/downloads/packages/>

根据个人的需要，下载Java Developers或者Enterprise Java Developers版本。

![](../images/post/20200323-01-down.png)

## 设置JDK

下载最新的JDK 14，并在Eclipse IDE中指向该JDK。


![](../images/post/20200323-02-jdk.png)

## 设置Maven

可选。如果项目是使用Maven管理，则建议下载最新的Maven，并在Eclipse IDE中指向该Maven。


![](../images/post/20200323-03-maven.png)

![](../images/post/20200323-04-maven.png)



## 设置网络代理

为了更快的下载插件，需要设置代理。

![](../images/post/20200323-05-proxy.png)


## 设置代码样式

可选。设置符合自己需求的代码样式。

![](../images/post/20200323-06-code-style.png)



![](../images/post/20200323-07-code-style.png)



## 设置字符

建议使用UTF-8。

![](../images/post/20200323-08-encoding.png)

## 安装支持Java 14的插件

由于当前的Eclipse还未正式支持Java 14，需要额外安装Java 14 Support for Eclipse 2020-03 (4.15)插件，用以支持Java 14。插件地址：
<https://marketplace.eclipse.org/content/java-14-support-eclipse-2020-03-415>


安装完成之后，就可以选择使用JDK 14的编译器，同时启用预览功能。

![](../images/post/20200323-09-plug.png)

## 编程

终于可以愉快的玩耍了Java 14了。


![](../images/post/20200323-10-coding.png)

本文所有源码可见<https://github.com/waylau/modern-java-demos>。


## 参考引用

* 本文同步至: <https://waylau.com/eclipse-ide-support-java14/>
* 现代Java案例大全：<https://github.com/waylau/modern-java-demos>
