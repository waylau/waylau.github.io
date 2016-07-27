---
layout: post
title: 实现在 Eclipse 中编写 Markdown 文件 
date: 2015-11-05 01:08
author: admin
comments: true
categories: [Eclipse,Markdown]
tags: [Eclipse,Markdown]
---

本文介绍了使用 Eclipse Markdown Editor 插件，来实现在 Eclipse 中编写 Markdown 文件。

<!-- more -->

## Eclipse Markdown Editor 的介绍

[Eclipse Markdown Editor](http://www.winterwell.com/software/markdown-editor.php) 是一款 Eclipse 插件，可以实现 .md 和 .txt 文件的 Markdown 语法高亮，并提供 HTML 预览。经过测试，可以满足一些基本的 Markdown 语法。

## 安装

与一般的 Eclipse 插件安装方法类似，无非是在线安装（Eclipse Marketplace）和离线包形式安装。

### 从 Eclipse Marketplace 安装

1. 在 Eclipse 中选择 (Help > Install New Software)
2. URL 为 http://winterwell.com/software/updatesite/
3. 选择最新版本

### 离线包形式安装

1. 下载插件包：<https://github.com/winterstein/Eclipse-Markdown-Editor-Plugin/releases>
2. 解压到本地，以 .link 文件方式来安装。其中 .link 指向了解压后的插件，而后把 .link 文件放到 Eclipse 安装目录的 dropins 目录下即可。可以参考<http://www.waylau.com/about-optimizer-for-eclipse/>

## 使用

插件安装成后，可以使用  Markdown Editor 查看、编辑 Markdown 文件

![](http://99btgc01.info/uploads/2015/11/002.jpg)

可以使用  Markdown View 预览 Markdown 文件

![](http://99btgc01.info/uploads/2015/11/001.jpg)

![](http://99btgc01.info/uploads/2015/11/003.jpg)

当然，与专业的 Markdown 编辑器来说，这款插件所支持的语法还是有限的，表格、删除线等暂时都未支持。

## 参考

* <http://www.winterwell.com/software/markdown-editor.php>
* <https://github.com/winterstein/Eclipse-Markdown-Editor-Plugin>
* <http://www.waylau.com/about-optimizer-for-eclipse/>