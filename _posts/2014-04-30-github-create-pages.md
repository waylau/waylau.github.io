---
layout: post
title: Github仓库发布成静态页面
date: 2014-04-30 16:46
author: admin
comments: true
categories: [Git]
tags: [Git, Github]
---
###Github仓库发布成静态页面

* 1.在仓库（repository）下点击“settings”
* 2.点击“Automatic Page Generator”
* 3.点击“Continue To Layouts”
* 4.选择主题，预览，点击“Publish”

此时，该仓库已经发布为静态网页，网址为“你的用户名.github.io/项目名”；
此时，在原来项目下会建立一个分支“gh-pages”,静态网页需上传到该分支下。
 
	cd repository
	git fetch origin
	git checkout gh-pages

<img src="http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=8a532a49b0fb43161e1f7a7f109f371e/54fbb2fb43166d22f8745f16442309f79052d235.jpg?referer=8dcd4535eb50352ae87611381eeb&x=.jpg" />

 
如果该仓库为用户站点（项目仓库为设定定为“你的用户名.github.com”），直接pull代码到master下就可以了，该网址为“你的用户名.github.io”。此功能可以用来搭建个人主页或者博客。

	cd repository
	git checkout master
	git pull origin master

<img src="http://b.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=edcd11b41f950a7b71354ec13aea13e4/6f061d950a7b0208da31756360d9f2d3562cc8bc.jpg?referer=caa6899669600c33a96eeaf82852&x=.jpg" />

 