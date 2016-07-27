---
layout: post
title: SVG Shapes之矩形 &lt;rect&gt;
date: 2014-05-04 12:01
author: admin
comments: true
categories: [Svg]
tags: [Svg,shape,rect]
---
##SVG 形状
SVG 有一些预定义的形状元素，可被开发者使用和操作：

* 矩形 &lt;rect&gt;
* 圆形 &lt;circle&gt;
* 椭圆 &lt;ellipse&gt;
* 线  &lt;line&gt;
* 折线 &lt;polygon&gt;
* 多边形 &lt;polyline&gt;
* 路径 &lt;path&gt;

##1.矩形 &lt;rect&gt;
####示例1.1
<svg width="400" height="110">
  <rect width="300" height="100" style="fill:rgb(0,0,255);stroke-width:3;stroke:rgb(0,0,0)" />
</svg>

	<svg width="400" height="110">
	  <rect width="300" height="100" style="fill:rgb(0,0,255);stroke-width:3;stroke:rgb(0,0,0)" />
	</svg>
**代码解释：**

* rect 元素的 width 和 height 属性可定义矩形的高度和宽度
* style 属性用来定义 CSS 属性
* CSS 的 fill 属性定义矩形的填充颜色（rgb 值、颜色名或者十六进制值）
* CSS 的 stroke-width 属性定义矩形边框的宽度
* CSS 的 stroke 属性定义矩形边框的颜色

####示例1.2
<svg width="400" height="180">
  <rect x="50" y="20" width="150" height="150"
  style="fill:blue;stroke:pink;stroke-width:5;fill-opacity:0.1;stroke-opacity:0.9" />
</svg>

	<svg width="400" height="180">
	  <rect x="50" y="20" width="150" height="150"
	  style="fill:blue;stroke:pink;stroke-width:5;fill-opacity:0.1;stroke-opacity:0.9" />
	</svg>
**代码解释：**

* x 属性定义矩形的左侧位置（例如，x="0" 定义矩形到浏览器窗口左侧的距离是 0px）
* y 属性定义矩形的顶端位置（例如，y="0" 定义矩形到浏览器窗口顶端的距离是 0px）
* CSS 的 fill-opacity 属性定义填充颜色透明度（合法的范围是：0 - 1）
* CSS 的 stroke-opacity 属性定义笔触颜色的透明度（合法的范围是：0 - 1）


####示例1.3
<svg width="400" height="180">
  <rect x="50" y="20" width="150" height="150"
  style="fill:blue;stroke:pink;stroke-width:5;opacity:0.5" />
</svg>

	<svg width="400" height="180">
	  <rect x="50" y="20" width="150" height="150"
	  style="fill:blue;stroke:pink;stroke-width:5;opacity:0.5" />
	</svg>
**代码解释：**

* CSS 的 opacity 属性定义整个元素的透明值（合法的范围是：0 - 1）
参考:
 
####示例1.4
<svg width="400" height="180">
  <rect x="50" y="20" rx="20" ry="20" width="150" height="150"
  style="fill:red;stroke:black;stroke-width:5;opacity:0.5" />
</svg>

	<svg width="400" height="180">
	  <rect x="50" y="20" rx="20" ry="20" width="150" height="150"
	  style="fill:red;stroke:black;stroke-width:5;opacity:0.5" />
	</svg>
**代码解释：**

* rx 和 ry 属性可使矩形产生圆角。

####示例1.5
<svg width="400" height="180">
   <rect x="50" y="70" width="35" height="20"
style="fill: yellow; fill-opacity: 0.5;
  stroke: green; stroke-width: 2; stroke-dasharray: 5 2"/>
</svg>

	<svg width="400" height="180">
	   <rect x="50" y="70" width="35" height="20"
		style="fill: yellow; fill-opacity: 0.5;
	  	stroke: green; stroke-width: 2; stroke-dasharray: 5 2"/>
	</svg>
**代码解释：**

* stroke-dasharray定义断续线，实线和虚线分别以5和2的长度交替显示。