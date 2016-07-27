---
layout: post
title: Help & Manual 导航生成失败--解决
date: 2014-10-27 01:40
author: admin
comments: true
categories: [Help]
tags: [HelpManual]
---
近期发现用 Help & Manual 5 来写帮助文档导航生成失败,
![失败](http://i1288.photobucket.com/albums/b484/waylau/man01_zps066772cb.png)
后测试发现，将项目名称`oem_0.9.2_help（2014.10.10）` 改为 `oem_0.9.2_help(2014.10.10)` 就好了~

![成功](http://i1288.photobucket.com/albums/b484/waylau/man02_zpsa932d563.png)

判断是项目名称中文字符不支持造成的。