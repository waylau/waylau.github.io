---
layout: post
title: Activiti 5 下载、安装和运行
date: 2014-12-12 01:21
author: admin
comments: true
categories: [Activiti]
tags: [Activiti]
---
 
##下载和安装

###activiti下载：

本例子版本：activiti-5.16.4.zip

[http://www.activiti.org/download.html](http://www.activiti.org/download.html)

###JDK 下载 JDK 6+

本例子版本：jdk7

[http://www.oracle.com/technetwork/java/javase/downloads/index.htm](http://www.oracle.com/technetwork/java/javase/downloads/index.htm)

<!-- more -->

###eclipse下载 Eclipse Indigo and Juno：

本例子版本：Eclipse Juno (4.2)

[http://www.eclipse.org/downloads/download.php?file=/technology/epp/downloads/release/juno/SR2/eclipse-jee-juno-SR2-win32.zip](http://www.eclipse.org/downloads/download.php?file=/technology/epp/downloads/release/juno/SR2/eclipse-jee-juno-SR2-win32.zip)
 
###解压 activiti-5.16.4.zip 获取 activiti-explorer.war

![](http://99btgc01.info/uploads/2014/12/01%282%29.jpg)

activiti-explorer.war 拷贝至 Tomcat（或其他 java web 服务器,本例为Tomcat 8.0）的 webapps 目录下

##运行

启动 tomcat，在浏览器打开项目（本例子 tomcat 端口为8089）

[http://localhost:8080/activiti-explorer/](http://localhost:8080/activiti-explorer/)
 
![](http://99btgc01.info/uploads/2014/12/02%283%29.jpg)


见到这个界面说明项目运行成功了!

默认使用的是内存数据库 H2，下面为数据库表

<table border="1" summary="The demo users"><colgroup><col><col><col></colgroup><thead><tr><th>用户 Id</th><th>密码</th><th>角色</th></tr></thead><tbody><tr><td>kermit</td><td>kermit</td><td>admin</td></tr><tr><td>gonzo</td><td>gonzo</td><td>manager</td></tr><tr><td>fozzie</td><td>fozzie</td><td>user</td></tr></tbody></table>


可以使用上面的账号登入系统（本例为kermit）

![](http://99btgc01.info/uploads/2014/12/03%283%29.jpg)

![](http://99btgc01.info/uploads/2014/12/04%283%29.jpg)

![](http://99btgc01.info/uploads/2014/12/05%282%29.jpg)

![](http://99btgc01.info/uploads/2014/12/06%282%29.jpg)

![](http://99btgc01.info/uploads/2014/12/07%282%29.jpg)

![](http://99btgc01.info/uploads/2014/12/08%282%29.jpg)


##扩展阅读

更详尽的用法，见 [Activiti 5.x 用户指南](https://github.com/waylau/activiti-5.x-user-guide)