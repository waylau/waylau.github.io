---
layout: post
title: 在 Tomcat 里面部署 Web 项目的几种方式 
date: 2015-03-14 01:41
author: admin
comments: true
categories: [Tomcat]
tags: [Tomcat]
---

1.直接把项目复制到 Tomcat 安装目录的 webapps 目录中，这是最简单的一种Tomcat项目部署的方法，也是初学者最常用的方法。

2.在 Tomcat 安装目录的 conf 文件夹，修改配置文件 server.xml，在`<host>`和`</host>`之间插入如下语句。

	<Context path="/emsc" docBase="D:\emsc" debug="0" privileged="true"></Context>

其中，docBase 为设置项目的路径。

将 path 设为 "/" ，则以无项目名称形式部署、访问

3.在 conf 目录中，在`Catalina\localhost`（此处需要注意目录的大小写）目录下新建一个XML文件，任意命名，只要和当前文件中的文件名不重复即可，代码如下。

	<Context path="/emsc" docBase="D:\emsc" debug="0" privileged="true"></Context>
