---
layout: post
title: 自定义Flex error tip 样式
date: 2013-10-12 02:47
author: admin
comments: true
categories: [Flex]
---
核心代码

	s=”该分类下没有应用”;
	myTip = ToolTipManager.createToolTip(s,event.currentTarget.x + event.currentTarget.width,   event.currentTarget.y+50,null, IUIComponent(event.currentTarget)) as ToolTip;
	myTip.setStyle(“styleName”, “errorTip”);
	myTip.width = 135;
	myTip.height = 55样式 <fx:Style>
	@namespace s “library://ns.adobe.com/flex/spark”;
	@namespace mx “library://ns.adobe.com/flex/mx”;
	.errorTip {
	borderColor: #1F1F27;
	backgroundColor :#1F1F27;
	color: #ffffff;
	fontFamily: Base02Embedded;
	fontSize: 12;
	fontWeight: normal;
	}
	  </fx:Style>