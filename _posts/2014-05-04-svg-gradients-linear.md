---
layout: post
title: SVG Gradients之Linear
date: 2014-05-04 17:28
author: admin
comments: true
categories: [Svg]
tags: [Svg,Gradient]
---
##SVG Gradients（渐变）
渐变是指从一种颜色向另外一种颜色的平滑转换。几种颜色转化能同时作用于同一个元素中。

SVG中主要有两种渐变:

* Linear （线性渐变）
* Radial （径向渐变）

##SVG &lt;linearGradient&gt; 
&lt;linearGradient&gt;元素必须嵌套在&lt;defs&gt;中

&lt;linearGradient&gt;可以定义成水平、垂直、任意角度渐变：

* 水平渐变：y1 、y2相同， x1、x2 不同
* 垂直渐变：x1、x2 相同， y1 、y2不同
* 垂直渐变：x1、x2 不同， y1 、y2不同

###示例1
水平渐变，从yellow 到 red

<svg height="150" width="400">
  <defs>
<linearGradient id="grad1" x1="0%" y1="0%" x2="100%" y2="0%">
  <stop offset="0%" style="stop-color:rgb(255,255,0);stop-opacity:1" />
  <stop offset="100%" style="stop-color:rgb(255,0,0);stop-opacity:1" />
</linearGradient>
  </defs>
  <ellipse cx="200" cy="70" rx="85" ry="55" fill="url(#grad1)" />
</svg>

	<svg height="150" width="400">
	  <defs>
	    <linearGradient id="grad1" x1="0%" y1="0%" x2="100%" y2="0%">
	      <stop offset="0%" style="stop-color:rgb(255,255,0);stop-opacity:1" />
	      <stop offset="100%" style="stop-color:rgb(255,0,0);stop-opacity:1" />
	    </linearGradient>
	  </defs>
	  <ellipse cx="200" cy="70" rx="85" ry="55" fill="url(#grad1)" />
	</svg>

**代码说明：**

* &lt;linearGradient&gt;中的x1, x2, y1,y2分别定义了渐变的起止位置
* 渐变可以设置多个颜色，每种颜色都用&lt;stop&gt;指定

###示例2
垂直渐变，从yellow 到 red

<svg height="150" width="400">
  <defs>
<linearGradient id="grad2" x1="0%" y1="0%" x2="0%" y2="100%">
  <stop offset="0%" style="stop-color:rgb(255,255,0);stop-opacity:1" />
  <stop offset="100%" style="stop-color:rgb(255,0,0);stop-opacity:1" />
</linearGradient>
  </defs>
  <ellipse cx="200" cy="70" rx="85" ry="55" fill="url(#grad2)" />
</svg>

	<svg height="150" width="400">
	  <defs>
	    <linearGradient id="grad2" x1="0%" y1="0%" x2="0%" y2="100%">
	      <stop offset="0%" style="stop-color:rgb(255,255,0);stop-opacity:1" />
	      <stop offset="100%" style="stop-color:rgb(255,0,0);stop-opacity:1" />
	    </linearGradient>
	  </defs>
	  <ellipse cx="200" cy="70" rx="85" ry="55" fill="url(#grad2)" />
	</svg>

###示例3
水平渐变，从yellow 到 red，并增加了一个文本

<svg height="150" width="400">
  <defs>
<linearGradient id="grad3" x1="0%" y1="0%" x2="100%" y2="0%">
  <stop offset="0%" style="stop-color:rgb(255,255,0);stop-opacity:1" />
  <stop offset="100%" style="stop-color:rgb(255,0,0);stop-opacity:1" />
</linearGradient>
  </defs>
  <ellipse cx="200" cy="70" rx="85" ry="55" fill="url(#grad3)" />
  <text fill="#ffffff" font-size="45" font-family="Verdana" x="150" y="86">
  SVG</text>
</svg>

	<svg height="150" width="400">
	  <defs>
	    <linearGradient id="grad3" x1="0%" y1="0%" x2="100%" y2="0%">
	      <stop offset="0%" style="stop-color:rgb(255,255,0);stop-opacity:1" />
	      <stop offset="100%" style="stop-color:rgb(255,0,0);stop-opacity:1" />
	    </linearGradient>
	  </defs>
	  <ellipse cx="200" cy="70" rx="85" ry="55" fill="url(#grad3)" />
	  <text fill="#ffffff" font-size="45" font-family="Verdana" x="150" y="86">
	  SVG</text>
	</svg>
