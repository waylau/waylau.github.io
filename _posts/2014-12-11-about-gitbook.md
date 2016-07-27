---
layout: post
title: 使用 GitBook 写开源书
date: 2014-12-11 01:21
author: admin
comments: true
categories: [GitBook]
tags: [Markdown,GitBook]
---

[GitBook](http://www.gitbook.com/) 让你在保持 使用 Markdown 的书写习惯外，稍加配置，就能自动发布到 GitBook 上，形成界面漂亮的各种电子书了（支持 html, pdf , ePUB, MOBI 等）。

##Usage 1 用法一 使用模板快速开始

[https://github.com/waylau/gitbook](https://github.com/waylau/gitbook) 这是个快速开始 GitBook 的模板项目,根据项目的结构，稍作修改，就能生成自己的电子书。

###Installation 安装

gitbook 1.x 时执行：

    $ npm install gitbook -g
  	
而从 gitbook 2.x 开始，执行下面新的命令语句：

    $ npm install gitbook-cli -g

###Checkout the repository 检出模板项目

    $ git clone https://github.com/waylau/gitbook.git
  
<!-- more -->

###Build 编译

	$ gitbook build ./
	
This is a `_book` directory was created.

![](http://99btgc01.info/uploads/2014/12/02%282%29.jpg)

###Run 运行

run the `index.html` in the `_book` directory .
 
打开该目录，右键运行 index.html 即可看到 gitbook 的效果

![](http://99btgc01.info/uploads/2014/12/03%282%29.jpg)

![](http://99btgc01.info/uploads/2014/12/04%282%29.jpg)

##Usage 2 用法二 手动创建项目

###Create a SUMMARY.md file 编辑 SUMMARY.md 文件

Edit content like:

	# Summary
	
	This is the summary of my book.
	
	* section 1
	    * [example 1](section1/example1.md)
	    * [example 2](section1/example2.md)
	* [section 2](section2/README.md)
	    * [example 1](section2/example1.md)
	   
###Create the directories and files for a book from its SUMMARY.md file using 根据 SUMMARY.md 自动生成目录结构

	$ gitbook init

###Build 编译

	$ gitbook build ./

###Run 运行