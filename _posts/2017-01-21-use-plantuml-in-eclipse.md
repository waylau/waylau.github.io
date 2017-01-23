---
layout: post
title: 在 Eclipse 里使用 PlantUML
date: 2017-01-21 00:22
author: admin
comments: true
categories: [PlantUML,Eclipse]
tags: [PlantUML,Eclipse,Graphviz]
---

本文介绍了如果在 Eclipse 中使用 PlantUML 插件，来进行常用 UML 图的绘制。
   
<!-- more -->

## PlantUML 简介

PlantUML 是一个开源项目，通过简单并带指引性语言定义来定义各种视图。该工具支持快速绘制以下 UML 图：

* 时序图
* 用例图
* 类图
* 活动图
* 组件图
* 状态图
* 部署图
* 对象图
* 线框图形界面

PlantUML 支持多个中 IDE 的集成，比如 Eclipse、NetBeans、Intellij idea 等。

## 安装 PlantUML for Eclipse 插件

PlantUML for Eclipse 插件主要用于在 Eclipse 中使用 PlantUML。在 Eclipse 的插件市场中安装，点击 “Help/Install new software”，在 Eclipse 4 (Juno) 及以上版本中，插件安装地址为：

* http://plantuml.sourceforge.net/updatesitejuno/
* http://basar.idi.ntnu.no/svn/tdt4100/anonymous/trunk/updatesite/


点击“Window/Show View/Other..”，可以将 PlantUML 面板显示出来。

## 安装 Graphviz

下载地址：http://www.graphviz.org/Download_windows.php

## PlantUML 的使用

@startuml
skinparam handwritten true

skinparam usecase {
	BackgroundColor DarkSeaGreen
	BorderColor DarkSlateGray

	BackgroundColor<< Main >> YellowGreen
	BorderColor<< Main >> YellowGreen
	
	ArrowColor Olive
	ActorBorderColor black
	ActorFontName Courier

	ActorBackgroundColor<< Human >> Gold
}

User << Human >>
:Main Database: as MySql << Application >>
(Start) << One Shot >>
(Use the application) as (Use) << Main >>

User -> (Start)
User --> (Use)

MySql --> (Use)

@enduml

## 参考资料

* http://plantuml.com/eclipse
* http://www.graphviz.org/content/plantuml-graphviz-dot-executable-usrbindot-does-not-excist
