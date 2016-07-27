---
layout: post
title: 使用 Eclipse 优化工具 Optimizer for Eclipse 
date: 2015-03-19 01:41
author: admin
comments: true
categories: [Eclipse]
tags: [Eclipse,Optimizer]
---
 
##Optimizer for Eclipse 介绍

官网： <http://zeroturnaround.com/free/optimizer-for-eclipse/>

顾名思义,Optimizer for Eclipse 这个插件主要是解决 Eclipse 性能优化问题：

###1.低内存分配

较低的最小堆大小不断将导致 JVM 分配额外的内存。当你没有足够的内存,垃圾收集频繁的 JVM 会减慢你的体验。

###2.慢类验证

类在 Eclipse 执行验证类和插件类显著减慢 JVM 的启动。

###3.大索引和历史

随着时间的推移,Eclipse 会生成一个庞大的历史和索引文件缓存。这些文件在 RAM 和 SSD 硬盘使 Eclipse 迟缓,反应迟钝。

<!-- more -->

###4.JDK 缓慢

Oracle JDK 是性能的领导者,并继续改善每一次释放。如果你不使用这个版本,那你就错过了。

###5.eclipse 过时了

Eclipse 是努力获得更好的与每一个新版本。保持您的 Eclipse 更新并且受益于最新的改进。

###6.冗长的构建和部署

JRebel 添加到您的 Eclipse 将允许您立即重新加载代码更改正在运行的应用程序,消除在应用程序构建时浪费的时间,重启和重新部署。

##Optimizer for Eclipse 安装

###方式1

点击首页“Get your Optimizer now”，拖动“Install Optimizer for Eclipse” 按钮到你的 Eclipse 即可，它会自动安装

![](http://99btgc01.info/uploads/2015/03/ofe00.jpg)

###方式2 

在 “Open Help → Eclipse Marketplace” 中搜 “Optimizer for Eclipse”。点击 “Install”

###方式3

在 “Open Help → Install New Software“ 添加一个 repository 

复制

	http://update.zeroturnaround.com/free-tools/site/

到对话框，而后进行安装。

###方式4

获取离线包<http://update.zeroturnaround.com/free-tools/site/update-site.zip>。解压到本地，以 .link 文件方式来安装。

其中 .link 指向了解压后的插件，而后把 .link 文件放到 Eclipse 安装目录的 dropins 目录下即可

![](http://99btgc01.info/uploads/2015/03/ofe000.jpg)

##Optimizer for Eclipse 使用

右手边有相应的需要优化的选项，

![](http://99btgc01.info/uploads/2015/03/ofe001.jpg)

点击选项后，再点击"Restart Eclipse",重启 Eclipse

![](http://99btgc01.info/uploads/2015/03/ofe002.jpg)

再次进入 Eclipse 时，可以看到性能提升的状况，你可以选择退出 Optimizer 或者继续优化

![](http://99btgc01.info/uploads/2015/03/ofe003.jpg)

可以在  “Open Help → Optimizer for Eclipse” 找到 Optimizer 的控制页面。

![](http://99btgc01.info/uploads/2015/03/ofe004.jpg)

##相关问题排查

###安装后无法启动

修改 eclipse.ini 文件，去除

	-javaagent:D:\eclipsePlugins\optimizer-for-eclipse\plugins\com.zeroturnaround.eclipse.optimizer.plugin_1.0.1.jar/agent/eclipse-optimizer-agent.jar

再启动，就好了