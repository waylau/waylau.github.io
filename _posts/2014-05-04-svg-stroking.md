---
layout: post
title: SVG Stroke 属性
date: 2014-05-04 12:09
author: admin
comments: true
categories: [Svg]
tags: [Svg,Stroke]
---

##9.Stroke 属性
SVG stroke拥有很多属性，下面只重点讲一部分：

###stroke

stroke用来定义line、text或者元素的outline等的颜色

<svg height="80" width="300">
  <g fill="none">
   <path stroke="red" d="M5 20 l215 0" />
   <path stroke="blue" d="M5 40 l215 0" />
   <path stroke="black" d="M5 60 l215 0" />
  </g>
</svg>

	<svg height="80" width="300">
	  <g fill="none">
	    <path stroke="red" d="M5 20 l215 0" />
	    <path stroke="blue" d="M5 40 l215 0" />
	    <path stroke="black" d="M5 60 l215 0" />
	  </g>
	</svg>
 
###stroke-width
stroke-width用来定义line、text或者元素的outline等的厚度

<svg height="80" width="300">
  <g fill="none" stroke="black">
   <path stroke-width="2" d="M5 20 l215 0" />
   <path stroke-width="4" d="M5 40 l215 0" />
   <path stroke-width="6" d="M5 60 l215 0" />
  </g>
</svg>

	<svg height="80" width="300">
	  <g fill="none" stroke="black">
	    <path stroke-width="2" d="M5 20 l215 0" />
	    <path stroke-width="4" d="M5 40 l215 0" />
	    <path stroke-width="6" d="M5 60 l215 0" />
	  </g>
	</svg>

###stroke-linecap
stroke-linecap定义线条结尾的不同样式

<svg height="80" width="300">
  <g fill="none" stroke="black" stroke-width="16">
   <path stroke-linecap="butt" d="M5 20 l215 0" />
   <path stroke-linecap="round" d="M5 40 l215 0" />
   <path stroke-linecap="square" d="M5 60 l215 0" />
  </g>
</svg>

	<svg height="80" width="300">
	  <g fill="none" stroke="black" stroke-width="16">
	    <path stroke-linecap="butt" d="M5 20 l215 0" />
	    <path stroke-linecap="round" d="M5 40 l215 0" />
	    <path stroke-linecap="square" d="M5 60 l215 0" />
	  </g>
	</svg>

###stroke-dasharray
stroke-dasharray定义断续线

<svg height="80" width="300">
  <g fill="none" stroke="black" stroke-width="4">
   <path stroke-dasharray="5,5" d="M5 20 l215 0" />
   <path stroke-dasharray="10,10" d="M5 40 l215 0" />
   <path stroke-dasharray="20,10,5,5,5,10" d="M5 60 l215 0" />
  </g>
</svg>

	<svg height="80" width="300">
	  <g fill="none" stroke="black" stroke-width="4">
	    <path stroke-dasharray="5,5" d="M5 20 l215 0" />
	    <path stroke-dasharray="10,10" d="M5 40 l215 0" />
	    <path stroke-dasharray="20,10,5,5,5,10" d="M5 60 l215 0" />
	  </g>
	</svg>

###stroke-opacity
stroke-opacity不透明度

<svg width="500" height="120">
<text x="22" y="40">欢迎光临www.waylau.com</text>
<path d="M20,40 l50,0"
   style="stroke: #00ff00;    fill:none;
   stroke-width:16px;
   stroke-opacity: 0.3;
   " />
<path d="M80,40 l50,0"
   style="stroke: #00ff00;    fill:none;
   stroke-width:16px;
   stroke-opacity: 0.7;
   " />
<path d="M140,40 l50,0"
   style="stroke: #00ff00;    fill:none;
   stroke-width:16px;
   stroke-opacity: 1;
   " />
</svg>

	<svg width="500" height="120">
    <text x="22" y="40">欢迎光临www.waylau.com</text>
    <path d="M20,40 l50,0"
          style="stroke: #00ff00;    fill:none;
                 stroke-width:16px;
                 stroke-opacity: 0.3;
                 " />
    <path d="M80,40 l50,0"
          style="stroke: #00ff00;    fill:none;
                 stroke-width:16px;
                 stroke-opacity: 0.7;
                 " />
    <path d="M140,40 l50,0"
          style="stroke: #00ff00;    fill:none;
                 stroke-width:16px;
                 stroke-opacity: 1;
                 " />
	</svg>

参考：<http://tutorials.jenkov.com/svg/stroke.html>
<http://www.w3schools.com/svg/svg_stroking.asp>