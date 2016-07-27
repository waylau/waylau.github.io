---
layout: post
title: 用 Github、Markdown 和 GitBook 写开源书
date: 2014-12-11 01:41
author: admin
comments: true
categories: [Github,GitBook]
tags: [Github,Markdown,GitBook]
---

之前一直是在 Github 上写开源书（见：[http://www.waylau.com/books/](http://www.waylau.com/books/)）但，由于 Github 本身的目录结构并不一定符合阅读的习惯，而且没有提供 pdf , ePUB, MOBI 等格式的转换下载。很多同学也还是习惯离线看文档。GitBook 就是解决这一问题。

GitBook 让你在保持在 Github 的书写习惯外，稍加配置，就能自动发布到GitBook 上，形成界面漂亮的电子书了（支持 html, pdf , ePUB, MOBI 等）。

<!-- more -->

如果你是第一次使用 GitBook,可以参见 [使用 GitBook 写开源书](http://www.waylau.com/about-gitbook/)

下面介绍，如果将一个 Github 项目转移到 GitBook 上来

##注册账户

需要拥有 [Github](https://github.com) 和 [GitBook](https://www.gitbook.com) 的账户

##关联账号

将 GitBook 的项目关联上  Github 的库

<a href="http://s1288.photobucket.com/user/waylau/media/waylau%20blog/gitbook/09_zpsaeaf67fe.jpg.html" target="_blank"><img src="http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/gitbook/09_zpsaeaf67fe.jpg" border="0" alt=" photo 09_zpsaeaf67fe.jpg"/></a>

<a href="http://s1288.photobucket.com/user/waylau/media/waylau%20blog/gitbook/10_zpsedcdd57a.jpg.html" target="_blank"><img src="http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/gitbook/10_zpsedcdd57a.jpg" border="0" alt=" photo 10_zpsedcdd57a.jpg"/></a>

<a href="http://s1288.photobucket.com/user/waylau/media/waylau%20blog/gitbook/15_zps799c92ae.jpg.html" target="_blank"><img src="http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/gitbook/15_zps799c92ae.jpg" border="0" alt=" photo 15_zps799c92ae.jpg"/></a>

##原有的项目

这个是原来的项目结构，参见[https://github.com/waylau/apache-shiro-1.2.x-reference](https://github.com/waylau/apache-shiro-1.2.x-reference)。

<a href="http://s1288.photobucket.com/user/waylau/media/waylau%20blog/gitbook/01_zpscf15bb6f.jpg.html" target="_blank"><img src="http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/gitbook/01_zpscf15bb6f.jpg" border="0" alt=" photo 01_zpscf15bb6f.jpg"/></a>

##添加 SUMMARY.md 文件配置

不改变原有的项目结构，添加 SUMMARY.md 文件，描述了 GitBook 目录结构，如下：

<a href="http://s1288.photobucket.com/user/waylau/media/waylau%20blog/gitbook/03_zpsc08a9a83.jpg.html" target="_blank"><img src="http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/gitbook/03_zpsc08a9a83.jpg" border="0" alt=" photo 03_zpsc08a9a83.jpg"/></a>

	# Summary
	
	This is the summary of my book.
	
	* I. Overview 总览
		* [1. Introduction 介绍](I. Overview 总览/1. Introduction 介绍.md) 
		* [2. Tutorial 教程](I. Overview 总览/2. Tutorial 教程.md) 
		* [3. Architecture 架构](I. Overview 总览/3. Architecture 架构.md) 
		* [4. Configuration 配置](I. Overview 总览/4. Configuration 配置.md) 
	* II. Core 核心
		* [5. Authentication 认证](II. Core 核心/5. Authentication 认证.md) 
		* [6. Authorization 授权](II. Core 核心/6. Authorization 授权.md) 
	* III. Web Applications
	* IV. Auxiliary Support 辅助支持
	* V. Integration 整合
	* VI. Tools 工具
	* VII. Index 目录
	* VIII. Other 其他

<a href="http://s1288.photobucket.com/user/waylau/media/waylau%20blog/gitbook/02_zps4c051456.jpg.html" target="_blank"><img src="http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/gitbook/02_zps4c051456.jpg" border="0" alt=" photo 02_zps4c051456.jpg"/></a>

##编译

	$ gitbook build ./

编译成功后，生成  `_book` 目录

<a href="http://s1288.photobucket.com/user/waylau/media/waylau%20blog/gitbook/04_zps2c62f5e9.jpg.html" target="_blank"><img src="http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/gitbook/04_zps2c62f5e9.jpg" border="0" alt=" photo 04_zps2c62f5e9.jpg"/></a>

##运行

运行  `_book` 目录 中 的`index.html`

<a href="http://s1288.photobucket.com/user/waylau/media/waylau%20blog/gitbook/05_zps05da7c9e.jpg.html" target="_blank"><img src="http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/gitbook/05_zps05da7c9e.jpg" border="0" alt=" photo 05_zps05da7c9e.jpg"/></a>

效果

<a href="http://s1288.photobucket.com/user/waylau/media/waylau%20blog/gitbook/06_zpsf5ef9805.jpg.html" target="_blank"><img src="http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/gitbook/06_zpsf5ef9805.jpg" border="0" alt=" photo 06_zpsf5ef9805.jpg"/></a>

##提交更新到 Github，自动发布到 GitBook

提交代码到 Github 的库，由于 GitBook 的项目关联上 Github 的库，所以 GitBook 会自动根据 Github 进行发布。
效果如下：
<a href="http://s1288.photobucket.com/user/waylau/media/waylau%20blog/gitbook/16_zpsae785ba0.jpg.html" target="_blank"><img src="http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/gitbook/16_zpsae785ba0.jpg" border="0" alt=" photo 16_zpsae785ba0.jpg"/></a>

在线电子书：[http://waylau.gitbooks.io/apache-shiro-1-2-x-reference/](http://waylau.gitbooks.io/apache-shiro-1-2-x-reference/)