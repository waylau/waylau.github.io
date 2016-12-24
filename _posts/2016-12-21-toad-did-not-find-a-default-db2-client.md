---
layout: post
title: ToadforDB2_Freeware_6.x 安装报错“Toad did not find a default DB2 client”
date: 2016-12-21 00:22
author: admin
comments: true
categories: [DB2]
tags: [DB2,ToadforDB2]
---

ToadforDB2 是比较好用的图形界面的DB2管理工具，分为免费版和商业版本。在新版的 ToadforDB2_Freeware_6.x 进行安装时，会提示报错“Toad did not find a default DB2 client”，即找不到  DB2 的客户端，意思是 6 版本区别与 5 版本 所不同的是，它依赖于 DB2 client 的实现。
   
<!-- more -->

有两种解决的方案：

1. 下载 5 版本的老的 ToadforDB2，但也就失去了尝试新事物的机会。
2. 使用 6 版本的 ToadforDB2 前，先安装  DB2 client ，本例采用的是官方的 “IBM Data Server Client”（<https://www.ibm.com/services/forms/preLogin.do?source=swg-idsc97>）。“IBM Data Server Client”安装包相对来说比较臃肿，大概 600M。其实 ToadforDB2 也才 90M 而已. 