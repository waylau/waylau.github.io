---
layout: post
title: HTML 颜色块图标
date: 2014-08-18 01:40
author: admin
comments: true
categories: [HTML]
tags: [HTML,CSS]
---

html颜色块图标醒目大方。

本文详解颜色块图标的CSS实现。

#设计界面元素
在把主页面的 html 的元素规定好。主要由四个图标组成。如下：
	
	<body>
	<div class="container">
	    <div class="bgblue"> 0#柴油<br/><h1>7.84</h1></div>
	    <div class="bgblue"> 90#柴油<br/><h1>8.26</h1></div>
	    <div class="bgblue"> 93#柴油<br/><h1>7.80</h1></div>
	    <div class="bgblue"> 97#柴油<br/><h1>8.45</h1></div>
	</div>
	
	<div id="buttom_id">
	    更多示例访问：<a href="http://wwww.waylau.com" target="_blank">http://wwww.waylau.com</a>
	</div>
	</body>

图标直接用的是`div` 

#样式
##图标样式
底色

	background:#123456;

字体颜色（白色）

 	color:#fff;

文本布局（垂直居中）

 	vertical-align: middle;
    text-align: center;
 	float: left;
    padding: 20px;
    margin-left: 20px;

效果
![整体效果](http://g.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=2ca8846b88d4b31cf43c94beb7ed5642/2f738bd4b31c8701c7ce2dce247f9e2f0708ff9a.jpg?referer=2553c73a4c4a20a4680909f73978&x=.jpg)

#圆形图标
可以将图标设置成圆形或者任意角度弧形
增加样式：

    border:4px solid;
    border-radius:55px;

其中	`border-radius`	控制`div`角度，`border`是边框

#整体效果

![整体效果2](http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=684d82201c30e924cba49c347c331f3b/29381f30e924b899c644e6106d061d950a7bf69a.jpg?referer=63b67dd278cb0a46dc35be093278&x=.jpg)
 

源码[下载](https://github.com/waylau/html-css-demo/blob/master/ColorTextDemo.html)


