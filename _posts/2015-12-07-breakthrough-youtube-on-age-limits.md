---
layout: post
title: 突破 YouTube 观影年龄的限制
date: 2015-12-07 01:08
author: admin
comments: true
categories: [Web]
tags: [YouTube]
---

YouTube 对视频进行了分级，某些“少儿不宜”的影片需要对用户的年龄进行判断，用户需要设置出生年月、提供手机验证等程序未免繁琐。本文介绍了一种简单的突破 YouTube 观影年龄的限制的方法。

<!-- more -->

## 年龄限制

比如，我们想研究下 `http://www.youtube.com/watch?v=dadasdadasd`
这个影片到底是个什么鬼，当我输入这个影片地址时，

![](http://99btgc01.info/uploads/2015/12/001.jpg)

如上图，当我们试图观看一部“少儿不宜”的影片时，它提示要进行“验证年龄”。尼玛，因为我翻了墙去到了韩国，竟然要我提供韩国的手机号，我去哪找棒子的手机。

## 解法

原始视频链接中的 <http://www.youtube.com/watch?v=dadasdadasd> ，dadasdadasd 就是这个影片的编号。

对没错，方法就是`http://www.youtube.com/embed/[影片的编号]`

这时候可以利用嵌入影片的手法绕过这个网页，浏览器输入

    http://www.youtube.com/embed/dadasdadasd
    
![](http://99btgc01.info/uploads/2015/12/002.jpg)

视频场面挺激烈的，行吧，就是这样。

## 参考

* [翻墙方法集合](https://github.com/waylau/books-collection)