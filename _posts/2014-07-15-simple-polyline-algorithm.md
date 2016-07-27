---
layout: post
title: 简单折线算法
date: 2014-07-15 07:08
author: admin
comments: true
categories: [Data visualization]
tags: [visualization,polyline]
---
 
#一、综述
在做流程图软件时，折线必不可少。本文展示的是现有系统的简单折线实现。折线主要分为一折折线、二折折线。下面例子默认起点为“图1”，终点为“图2”，假设pt1,pt2为已知点,pt3,pt4为动态计算点。

#二、一折折线
 ![一折折线](http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=4239278589d4b31cf43c94beb7ed5642/2f738bd4b31c8701a95f8e20257f9e2f0608ff0e.jpg?referer=4bc264d44d4a20a4680908f73904&x=.jpg)

	pt3 = ( pt2.x  , pt1.y )

#三、二折折线
##1.pt1 在pt2的左侧
 ![二折折线,pt1 在pt2的左侧](http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=b8c88ef7a38b87d65442ab1a37335905/8601a18b87d6277f95fb0efe2a381f30e824fc0e.jpg?referer=2b704fd50bf79052b608730e3c04&x=.jpg)

	pt3 = ( pt1.x + ( pt2.x – pt1.x )*0.5 , pt1.y )
	pt4 = ( pt1.x + ( pt2.x – pt1.x )*0.5 , pt2.y )

##2.pt1 在pt2的右侧
 ![二折折线,pt1 在pt2的右侧](http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=b8c88ef7a38b87d65442ab1a37335905/8601a18b87d6277f95fb0efe2a381f30e824fc0e.jpg?referer=2b704fd50bf79052b608730e3c04&x=.jpg)

	pt3 = ( pt2.x + ( pt1.x – pt2.x )*0.5 , pt1.y )
	pt4 = ( pt2.x + ( pt1.x – pt2.x )*0.5 , pt2.y )
