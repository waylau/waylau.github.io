---
layout: post
title: SQL Server取开始时间、截止时间
date: 2014-08-26 01:40
author: admin
comments: true
categories: [SQLServer]
tags: [SQLServer]
---
 
	--当天开始
	SELECT dateadd(ms,0,DATEADD(dd, DATEDIFF(dd,0,getdate()), 0))		当天开始
	--当天结束
	SELECT dateadd(ms,-3,DATEADD(dd, DATEDIFF(dd,-1,getdate()), 0))		当天结束
	
	--当月第一天
	SELECT DATEADD(mm, DATEDIFF(mm,0,getdate()), 0)				当月第一天
	--当月最后一天
	SELECT   dateadd(ms,-3,DATEADD(mm,   DATEDIFF(m,0,getdate())+1,   0))   当月最后一天

![时间](http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=7686612ae6dde711e3d243f397d4bf26/8435e5dde71190efd52243f0cd1b9d16fcfa60c0.jpg?referer=12d1d7e5818ba61e86f9fc1fc0de&x=.jpg)