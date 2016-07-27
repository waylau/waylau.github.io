---
layout: post
title: Tomcat设置，输入IP地址 ，直接访问项目主页
date: 2011-05-24 01:40
author: admin
comments: true
categories: [Tomcat]
tags: [Tomcat]
---
1.cmd 命令 看下 80 端口是否被占用
<pre>netstat -ano</pre>
2.没有被占用的话，将tomcat的端口设成80

<em id="__mceDel"> 配置文件的路径为\pache Software Foundation\Tomcat 6.0\conf\server.xml</em>
<pre>&lt;Connector port="80" protocol="HTTP/1.1" 
connectionTimeout="20000" 
redirectPort="8443" URIEncoding="UTF-8"/&gt;</pre>
3.修改tomcat配置,默认访问指定项目

在&lt;host /&gt;里面添加如下：
<pre>&lt;Context path="" docBase="D:\Program Files\Apache Software Foundation\Tomcat 6.0\webapps\abc" /&gt;</pre>
docBase 是你的项目路径

这样 ，直接输入IP地址，就会映射到你的项目的index.html

&nbsp;
