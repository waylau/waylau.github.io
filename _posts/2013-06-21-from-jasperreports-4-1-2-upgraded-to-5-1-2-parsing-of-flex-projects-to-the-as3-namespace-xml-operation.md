---
layout: post
title: 由“Jasperreports 4.1.2升级到5.1.2对flex项目的解析”到AS3 带命名空间的XML的操作
date: 2013-06-21 01:55
author: admin
comments: true
categories: [Jasperreports]
---
项目中，对Jasperreports-4.1.2核心包进行了升级，发现，前端flex无法对Jasperreports的格式进行解析了~

iReport 4.1 设计的模版可以解析，而iReport 4.6 设计的模版就不行了~

断点后，对比了从后台传过来的Jasperreports 的xml数据，发现存在差异

 

4.1.2包生成的xml数据jasperPrint标签为：

	<jasperPrint name="report13" pageWidth="842" pageHeight="842" topMargin="0" leftMargin="0" bottomMargin="0" rightMargin="0" locale="zh_CN" timezone="Asia/Shanghai">
5.1.2包生成的xml数据jasperPrint标签为：

	<jasperPrint xmlns="http://jasperreports.sourceforge.net/jasperreports/print"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://jasperreports.sourceforge.net/jasperreports/print
	http://jasperreports.sourceforge.net/xsd/jasperprint.xsd" name="report13"
	pageWidth="842" pageHeight="842" topMargin="0" leftMargin="0" bottomMargin="0"
	rightMargin="0" locale="zh_CN" timezone="Asia/Shanghai">
 

后期版本的Jasperreports核心包多生成了命名空间

而之前官方提供Jasperreports 的flex解析包net.sf.jasperreports.flex （来自Jasperreports的项目示例jasperreports-flash-4.0.0-project.zip）任然停留在2010年的版本未更新（估计Jasperreports放弃了对flash的支持），导致无法对新版本的xml数据进行解析

AS3对于带命名空间的XML的解析操作上有很大的差异，例如以下来自Jasperreports的ReportFactory.as

    var props:XMLList = xml.property;
			for each (var prop:XML in props)
			{
				if (prop.@name == "net.sf.jasperreports.export.xml.start.page.index")
				{
					report.startPageIndex = int(prop.@value);
				}
				if (prop.@name == "net.sf.jasperreports.export.xml.end.page.index")
				{
					report.endPageIndex = int(prop.@value);
				}
				if (prop.@name == "net.sf.jasperreports.export.xml.page.count")
				{
					report.pageCount = int(prop.@value);
				}
			}
 

由于新版本多了命名空间，所以 直接xml.property 是无法获取到property的。要带上命名空间
	
	var ns:Namespace = new Namespace("http://jasperreports.sourceforge.net/jasperreports/print");
	trace(xml.ns::property);    //输出带名字空间的XML
 
由于 涉及到多个 命名空间，这种方式在该项目中不合适，且要修改后续的其他操作。

最简单的方式，就是 干掉命名空间，这样后续操作不变。

这个就涉及到XML的操作了。查XML api发现了removeNamespace(ns:Namespace):XML方法，以为轻松搞定，实验后 ，发现 不行。默认的命名空间（如 xmlns=”http://jasperreports.sourceforge.net/jasperreports/print”）无法删除。


用xml.children()方法取他孩子，发现，每个孩子也带上了命名空间。

 

最后用上了正则表达式。限定类型的命名表达式可以删除了。而且用了两次。汗。。

           var str:String = oldxml.toString();

           var str2:String = str.replace(/xmlns(.*?)="(.*?)"/gm, "").replace(/xsi(.*?)="(.*?)"/gm, "");

            xml = new XML(str2);