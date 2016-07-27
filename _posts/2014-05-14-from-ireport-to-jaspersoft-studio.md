---
layout: post
title: 从iReport到Jaspersoft Studio
date: 2014-05-14 13:08
author: admin
comments: true
categories: [JasperReports]
tags: [java,report,Studio]
---
从5.5版本开始，Jaspersoft Studio将取代iReport 成为JasperReports官方设计器。iReport 维护截止日期到2015年底，意味着不会再有新的功能增加进iReport，但会做一些关键bug的修复、更新。所幸的是基于eclipse的Jaspersoft Studio同样开源、免费！Yeah！

Jaspersoft Studio是一个专为JasperReports报表引擎而开发的报表设计器，是iReport设计器的一个完整重构，基于Eclipse平台实现。它能够让你创建包含图表、图片、子报表、交叉表等非常复杂的布局。可以通过JDBC、TableModels、JavaBeans、XML、Hibernate、CSV或自定义数据源来读取数据。可以将报表输出成PDF、RTF、XML、XLS、CSV、HTML、XHTML、text、DOCX或OpenOffice。

##Report Life Cycle(报表生命周期)
iReport和JasperSoft Studio中的报表的生命周期是相同的。

当你使用iReport或JasperSoft Studio做报表时，要创建一个JRXML文件，该文件是包含报表布局定义的XML文档。布局是完全可视化，这样你就可以忽略JRXML文件的基础结构。执行报告之前，JRXML必须编译成名为Jasper的二进制文件。Jasper文件就是应用程序生成报表所需要的。

有许多数据源类型。您可以从SQL查询，XML文件，csv文件，HQL（Hibernate查询语言）查询，JavaBeans集合等。如果你没有一个合适的数据源，JasperReports的允许你编写自己的自定义数据源。通过Jasper文件和数据源，JasperReports 就能生成最终用户想要的文档格式。

iReport和JasperSoft Studio允许您配置数据源，并使用它们来测试您的报表。在许多情况下，数据驱动向导可以帮助您快速设计您的报表。iReport包括JasperReports引擎本身，可以让您预览报表输出，测试和优化您的报表。
 
![Report Life Cycle](http://community-static.jaspersoft.com/sites/default/files/images/reportLifeCycle.png)

##User Interface(用户界面)
JasperSoft Studio 有两个不同的版本：一个独立的RCP产品，以及Eclipse插件的版本。熟悉Eclipse的人都会对用户界面感到很熟悉，而那些新用户，或者那些只熟悉iReport的设计师，则会觉得显示元素的布局会出现很大的不同。独立和插件的版本也有类似的用户界面。在下面可以看到包括JasperSoft Studio界面的预览：

<img src="http://community-static.jaspersoft.com/sites/default/files/images/jssUI(1).jpg"/>

**Report editing area（主编辑区域）**中，您直观地通过拖动，定位，对齐和通过**Designer palette（设计器调色板）**对报表元素调整大小。

JasperSoft Studio有一个多标签编辑器，**Design**,**Source**和 **Preview**：

* **Design tab**：当你打开一个报告文件，它允许您以图形方式创建报表选中
* **Source tab**： 包含用于报表的JRXML源代码。
* **Preview tab**： 允许在选择数据源和输出格式后，运行报表预览。

很多页面可以查看数据：

* **Repository Explorer view**：包含JasperServer生成的连接和可用的数据适配器列表
* **Project Explorer view**：包含JasperReports的工程项目清单
* **Outline view**：在大纲视图中显示了一个树的形式的方式报告的完整结构。
* **Properties view**：通常是任何基于Eclipse的产品/插件的基础之一。它通常被填充与实际所选元素的属性的信息。这就是这样，当你从主设计区域（即：一个文本字段）选择一个报表元素或从大纲，视图显示了它的信息。其中一些属性可以是只读的，但大部分都是可编辑的，对其进行修改，通常会通知更改绘制的元素（如：元素的宽度或高度）。
* **Problems view**：显示的问题和错误，例如可以阻断报告的正确的编译。

最后，**Report state summary**提供了有关在报表编译/填充/执行统计用户有用的信息。错误会显示在这里。

下面是一个简短的比较表，帮助用户看到iReport和JasperSoft Studio的主要内容。


| iReport Designer | Jaspersoft Studio| 
| --------   | --------   |
| JasperServer Repository	| Repository Explorer| 
| Report Inspector	| Outline view| 
| Report Designer	| Report Editing Area| 
| Problems List	| Problems view| 
| Elements palette	| Designer Palette| 
| Formatting tools	| Available via context menu on the element| 
| Property sheet	| Properties view| 
| Styles library	| ---| 
| ---	| Project Explorer| 
| iReport Designer Output window| Report State summary| 

参考：<http://community.jaspersoft.com/wiki/introduction-jaspersoft-studio>
<http://community.jaspersoft.com/project/ireport-designer>