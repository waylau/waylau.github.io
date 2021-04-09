---
layout: post
title: 使用Maven Helper插件在IDEA中查看依赖树
date: 2021-02-16 00:22
author: admin
comments: true
categories: [IDEA,Maven]
tags: [IDEA,Maven]
---

默认打开pom,xml文件，是没有依赖的分析的。

<!-- more -->


 
![](https://communityfile-drcn.op.hicloud.com/FileServer/getFile/cmtybbs/042/413/002/0000000000042413002.20210402105823.49218720949021596367115124499436:50520408144750:2800:42F2AFC9F3B55152306DD3425D0F7E85BA649A19C92051CD30686261885935EE.png)




安装Maven Helper插件，该插件提供：



* 分析和排除冲突依赖关系的简单方法
* 为包含当前文件的模块或根模块运行/调试maven目标的操作
* 在当前maven模块路径上打开终端的操作
* 运行/调试当前测试文件的操作。




再次打开pom文件，可以看到左下角多了个Dependency Analyzer，通过Dependency Analyzer可以查看依赖关系树。



![](https://communityfile-drcn.op.hicloud.com/FileServer/getFile/cmtybbs/042/413/002/0000000000042413002.20210402105839.67510315452826248354360900942042:50520408144750:2800:77759EA83002CDC0502B48EBE475B5CA3B38AD0081A232A2A6E1DE46E9466787.png)