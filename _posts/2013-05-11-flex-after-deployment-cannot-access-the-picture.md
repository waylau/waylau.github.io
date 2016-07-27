---
layout: post
title: Flex4.6项目本地右键编译正常，部署后访问不了图片资源。解决
date: 2013-05-11 01:38
author: admin
comments: true
categories: [Tomcat]
tags: [Tomcat,编码]
---
###问题：
如题
遇到 一个 奇怪的 问题，
项目右键运行 图片显示正常。
但 部署到 web服务器 就 显示不正常了。

PS:图片命名用了中文。
用了英文的貌似显示 都正常。

###解决
初步 认定 可能是 web 服务器 编码所致使

在tomcat/conf/server.xml中找到 Connector 在后面加上 URIEncoding="UTF-8" 即可

	<Connector port="8089" protocol="HTTP/1.1" 
               connectionTimeout="20000" 
               redirectPort="8443"  URIEncoding="UTF-8"/>
 
