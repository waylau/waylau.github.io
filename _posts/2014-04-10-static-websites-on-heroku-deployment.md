---
layout: post
title: 在Heroku上部署静态网站
date: 2014-04-10 20:46
author: admin
comments: true
categories: [Web]
tags: [heroku, Web]
---
静态网页是无法直接部署在Heroku的，push代码也报错。

解决方法：增加一个index.php，包裹html将静态网站伪装成php网站即可，代码如下：

	 <?php
	    include_once("index.html");
	?>

此时，push 代码，Heroku会自动生成php框架。

<img class="alignnone" alt="" src="http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=0e5ac25a808ba61edbeec82a710fe637/32fa828ba61ea8d34270b094950a304e241f58d4.jpg?referer=12012c6453da81cb17f1b7fd98ca&amp;x=.jpg" width="550" height="144" />
