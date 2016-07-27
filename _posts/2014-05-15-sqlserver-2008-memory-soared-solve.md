---
layout: post
title: SQLServer2008内存飙升 解决
date: 2014-05-15 07:08
author: admin
comments: true
categories: [SQLServer]
tags: [SQLServer]
---

SQLServer2008在运行一段时间后内存飙升，严重导致系统奔溃
![SQLServer.exe](http://e.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=d7311b4cb0119313c343ffb555037dea/faf2b2119313b07e4ef285860ed7912396dd8cf5.jpg?referer=149d9678a5efce1bb33cfcfaecab&x=.jpg)

这个是SQLServer内存管理机制决定的，由于他应用了内存池的技术，有效提高数据库的查询速度，但同时会占用大量的内存。详见SQLServer 
<a href="http://technet.microsoft.com/zh-cn/library/cc280359(v=sql.105).aspx" target="blank">内存管理体系结构</a>

默认设置，他会占用计算机的所有内存。
![SQLServer momery](http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=e29e6aad612762d0843ea4ba90d779c7/8b13632762d0f703dafd40520afa513d2797c5f5.jpg?referer=6e5f34942a34349b2d115ab523ab&x=.jpg)

所以要限制内存池最高内存占用量。

* 1.登陆数据库管理工具
* 2.“右键”数据库连接
* 3.点击“属性”选项
* 4.选中“内存”
* 5.设置“最大服务器内存”

![限制内存](http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=e7ad6dae03e9390152028d3b4bd725da/8c1001e93901213f237a9b5256e736d12e2e95f7.jpg?referer=92573dea544e9258ff23b2ded3ad&x=.jpg)

 