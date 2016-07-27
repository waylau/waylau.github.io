---
layout: post
title: HTML 透明登录框的实现
date: 2014-08-17 01:40
author: admin
comments: true
categories: [HTML]
tags: [HTML,CSS]
---

html登录界面，看似简单，无非就是一个底图，一个名称，一个登录，一些底部信息，在没有美工的情况下，纯代码实现还是有些坑。

本文详解透明的登录框的CSS实现。

#设计界面元素
在把主页面的 html 的元素规定好。主要由底图、名称、登录表单、底部信息组成。如下：
	
	<img  src="img/bg.jpg"  />
	<div id="login_id">
	    <h1>wwww.waylau.com<br/>信息管理系统</h1>
	    <form id="login_form">
	        <input name="loginname" type="text" class="loginuser"    placeholder="用户名"   />
	        <input name="password" type="password" class="loginpwd"   placeholder="密码"  />
	        <input id="login" type="button" class="loginbtn" value="登录" />
	    </form>
	</div>
	
	<div id="buttom_id">
	    版权所有：<a href="http://wwww.waylau.com" target="_blank">http://wwww.waylau.com</a>
	</div>

`body` 的 底图我的需求是整张图拉伸铺满，而不是重复显示，或者留空。虽然 `body` 的  `background：url(xxx)` 可以插入底图，但是 设为`no-repeat`的话，如果图太小，会留空，`background-size`设置没有达到预期效果。所以直接用了`img`.

#样式
##整体样式
整体是居中的

	body {
	    text-align: center;
	    margin: 0;
	}

##底图的样式

	img {
	    width:100% ;height:100%;position:absolute;
		top:0;left:0;right:100;bottom:500;z-index:-1;
	}

拉伸图片填充整个界面，其中`z-index:-1;` 就是让图片的深度降一级，意思是说，图片是在所有元素的后面。

##表单样式
输入表单，我的需求是要透明（正常的`input`简直俗透了），只显示一个底部输入线条

	#login_form input {
	    border: 0;
	    BACKGROUND-COLOR: transparent;
	    BORDER-BOTTOM: #ffffff 1px solid;
	    BORDER-LEFT: #ffffff 1px solid;
	    COLOR: #ffffff;
	    HEIGHT: 18px;
	    font-size: 12pt
	}

`border: 0;`就是不要显示输入框的边框。

`BACKGROUND-COLOR: transparent;`这里是设置了透明。

`BORDER-BOTTOM`就是 用来显示`底线`的，同理`BORDER-LEFT`、` BORDER-RIGHT`、`BORDER-TOP`就是设置上下左右的边框线条。

#整体效果

![整体效果](http://e.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=b3141eb3452309f7e36fad1742357dce/21a4462309f79052647c4c190ff3d7ca7bcbd5b2.jpg?referer=ec666d54f8f2b211bd39b07e1350&x=.jpg)

么么滴，相册的水印真讨厌。╯﹏╰ 

另外底图来自《星际争霸》原画，当年最爱的游戏Y(^_^)Y 

源码[下载](https://github.com/waylau/html-css-demo/blob/master/TransparentLoginBoxDemo.html)


