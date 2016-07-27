---
layout: post
title: eclipse中"Tomcat v8.0 Server at localhost failed to start"解决以及安装方法
date: 2014-08-01 01:40
author: admin
comments: true
categories: [Tomcat]
tags: [Tomcat]
---

#设置环境变量
变量名：CATALINA_HOME
变量值：tomcat 安装的路径,如 “C:\apache-tomcat-8.0.9”


#安装 service服务 ：

打开命令行提示符窗口

=> 进入Tomcat安装目录==> 进入bin目录下==> 
输入：`service.bat install` 即可

![service](http://e.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=dd9b5538b23533faf1b6932b98e88c22/bd3eb13533fa828b625d41d5fe1f4134970a5a8e.jpg?referer=cab7f2d3ae6eddc47ff081cba684&x=.jpg)

#在eclipse里面配置tomcat，如下图：

![配置](http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=5c5b7e2dd70735fa95f04ebcae6a7e8e/962bd40735fae6cd8cca77570cb30f2442a70f8e.jpg?referer=5e34d014b01bb051d63386186985&x=.jpg)