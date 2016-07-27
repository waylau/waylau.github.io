---
layout: post
title: Java版本号处理-split 点号 split(".") 
date: 2014-09-24 01:40
author: admin
comments: true
categories: [Java]
tags: [Java,split]
---
近期在做一个版本号的判断，在使用 Java split() 方法时，希望把版本号中的数字组成数组。很自然的，我用了 split(".") 来分割成数组，结果不行。

	String v = "1.0.1";
	String[] vs = v.split(".");
	
	int len = vs.length;
	
	for(int i = 0; i<len; i ++){
		System.out.println(vs[i]);
	}

后查 API，得知 split　的参数是　String regex
代表的是一个正则表达式。如果是正则中的特殊字符，就不能了。点正好是一个特殊字符。如下方式解决：

	String v = "1.0.1";
	String[] vs = v.split("[.]");
	
	int len = vs.length;
	
	for(int i = 0; i<len; i ++){
		System.out.println(vs[i]);
	}