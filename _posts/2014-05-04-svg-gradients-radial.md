---
layout: post
title: SVG Gradients之Radial
date: 2014-05-04 21:28
author: admin
comments: true
categories: [Svg]
tags: [Svg,Gradient]
---

##SVG &lt;radialGradient&gt; 
&lt;radialGradient&gt;元素必须嵌套在&lt;defs&gt;中



###示例1
径向渐变，从 white  到 blue

<svg height="150" width="500">
  <defs>
<radialGradient id="grad1" cx="50%" cy="50%" r="50%" fx="50%" fy="50%">
  <stop offset="0%" style="stop-color:rgb(255,255,255);
  stop-opacity:0" />
  <stop offset="100%" style="stop-color:rgb(0,0,255);stop-opacity:1" />
</radialGradient>
  </defs>
  <ellipse cx="200" cy="70" rx="85" ry="55" fill="url(#grad1)" />
</svg>

	<svg height="150" width="500">
	  <defs>
	    <radialGradient id="grad1" cx="50%" cy="50%" r="50%" fx="50%" fy="50%">
	      <stop offset="0%" style="stop-color:rgb(255,255,255);
	      stop-opacity:0" />
	      <stop offset="100%" style="stop-color:rgb(0,0,255);stop-opacity:1" />
	    </radialGradient>
	  </defs>
	  <ellipse cx="200" cy="70" rx="85" ry="55" fill="url(#grad1)" />
	</svg>

**代码说明：**

* cx, cy,r 定义的是外层的圆，fx ,fy定义的是内层的圆
* 渐变可以设置多个颜色，每种颜色都用&lt;stop&gt;指定
* offset定义了渐变颜色的起止

###示例2

<svg height="150" width="500">
  <defs>
<radialGradient id="grad2" cx="20%" cy="30%" r="30%" fx="50%" fy="50%">
  <stop offset="0%" style="stop-color:rgb(255,255,255);
  stop-opacity:0" />
  <stop offset="100%" style="stop-color:rgb(0,0,255);stop-opacity:1" />
</radialGradient>
  </defs>
  <ellipse cx="200" cy="70" rx="85" ry="55" fill="url(#grad2)" />
</svg>

	<svg height="150" width="500">
	  <defs>
	    <radialGradient id="grad2" cx="20%" cy="30%" r="30%" fx="50%" fy="50%">
	      <stop offset="0%" style="stop-color:rgb(255,255,255);
	      stop-opacity:0" />
	      <stop offset="100%" style="stop-color:rgb(0,0,255);stop-opacity:1" />
	    </radialGradient>
	  </defs>
	  <ellipse cx="200" cy="70" rx="85" ry="55" fill="url(#grad2)" />
	</svg> 