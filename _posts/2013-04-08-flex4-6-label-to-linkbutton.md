---
layout: post
title: Flex4.6 用Lable自定义LinkButton
date: 2013-04-08 01:28
author: admin
comments: true
categories: [Flex]
tags: [Flex]
---
很简单，先是继承Lable ,而后 设置相关属性，比如 小手鼠标、下划线等

	package com.waylau.eagleos.components
	{
		import spark.components.Label;
	
		public class LinkButton extends Label
		{
			public function LinkButton()
			{
				super();
	
				this.useHandCursor = true ;
				this.buttonMode = true ;
				this.mouseChildren = false ;
	
				this.setStyle("textDecoration","underline");
			}
		}
	}
