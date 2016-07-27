---
layout: post
title: SVG Filters之阴影Drop Shadows
date: 2014-05-04 14:28
author: admin
comments: true
categories: [Svg]
tags: [Svg,Filter]
---
##SVG &lt;feOffset&gt; 

###示例1

<svg height="120" width="120">
  <defs>
   <filter id="f1" x="0" y="0" width="200%" height="200%">
   <feOffset result="offOut" in="SourceGraphic" dx="20" dy="20" />
   <feBlend in="SourceGraphic" in2="offOut" mode="normal" />
   </filter>
  </defs>
  <rect width="90" height="90" stroke="green" stroke-width="3"
  fill="yellow" filter="url(#f1)" />
</svg>

	<svg height="120" width="120">
	  <defs>
	    <filter id="f1" x="0" y="0" width="200%" height="200%">
	      <feOffset result="offOut" in="SourceGraphic" dx="20" dy="20" />
	      <feBlend in="SourceGraphic" in2="offOut" mode="normal" />
	    </filter>
	  </defs>
	  <rect width="90" height="90" stroke="green" stroke-width="3"
	  fill="yellow" filter="url(#f1)" />
	</svg>

**代码解释：**

* 该<filter>元素的id属性定义了滤镜的唯一名称
* 该<rect>元件的滤镜属性链接到了“f1”滤镜

###示例2
加上了 &lt;feGaussianBlur&gt; 

<svg height="140" width="140">
  <defs>
<filter id="f2" x="0" y="0" width="200%" height="200%">
  <feOffset result="offOut" in="SourceGraphic" dx="20" dy="20" />
  <feGaussianBlur result="blurOut" in="offOut" stdDeviation="10" />
  <feBlend in="SourceGraphic" in2="blurOut" mode="normal" />
</filter>
  </defs>
  <rect width="90" height="90" stroke="green" stroke-width="3"
  fill="yellow" filter="url(#f2)" />
</svg>

	<svg height="140" width="140">
	  <defs>
	    <filter id="f2" x="0" y="0" width="200%" height="200%">
	      <feOffset result="offOut" in="SourceGraphic" dx="20" dy="20" />
	      <feGaussianBlur result="blurOut" in="offOut" stdDeviation="10" />
	      <feBlend in="SourceGraphic" in2="blurOut" mode="normal" />
	    </filter>
	  </defs>
	  <rect width="90" height="90" stroke="green" stroke-width="3"
	  fill="yellow" filter="url(#f2)" />
	</svg>

###示例3

<svg height="140" width="140">
  <defs>
<filter id="f3" x="0" y="0" width="200%" height="200%">
  <feOffset result="offOut" in="SourceAlpha" dx="20" dy="20" />
  <feGaussianBlur result="blurOut" in="offOut" stdDeviation="10" />
  <feBlend in="SourceGraphic" in2="blurOut" mode="normal" />
</filter>
  </defs>
  <rect width="90" height="90" stroke="green" stroke-width="3"
  fill="yellow" filter="url(#f3)" />
</svg>

	<svg height="140" width="140">
	  <defs>
	    <filter id="f3" x="0" y="0" width="200%" height="200%">
	      <feOffset result="offOut" in="SourceAlpha" dx="20" dy="20" />
	      <feGaussianBlur result="blurOut" in="offOut" stdDeviation="10" />
	      <feBlend in="SourceGraphic" in2="blurOut" mode="normal" />
	    </filter>
	  </defs>
	  <rect width="90" height="90" stroke="green" stroke-width="3"
	  fill="yellow" filter="url(#f3)" />
	</svg>

**代码解释：**

* &lt;feOffset&gt; 元素改成了"SourceAlpha"虚化中的Alpha通道代替了RGBA像素

###示例4

<svg height="140" width="140">
  <defs>
<filter id="f4" x="0" y="0" width="200%" height="200%">
  <feOffset result="offOut" in="SourceGraphic" dx="20" dy="20" />
  <feColorMatrix result="matrixOut" in="offOut" type="matrix"
  values="0.2 0 0 0 0 0 0.2 0 0 0 0 0 0.2 0 0 0 0 0 1 0" />
  <feGaussianBlur result="blurOut" in="matrixOut" stdDeviation="10" />
  <feBlend in="SourceGraphic" in2="blurOut" mode="normal" />
</filter>
  </defs>
  <rect width="90" height="90" stroke="green" stroke-width="3"
  fill="yellow" filter="url(#f4)" />
</svg>

	<svg height="140" width="140">
	  <defs>
	    <filter id="f4" x="0" y="0" width="200%" height="200%">
	      <feOffset result="offOut" in="SourceGraphic" dx="20" dy="20" />
	      <feColorMatrix result="matrixOut" in="offOut" type="matrix"
	      values="0.2 0 0 0 0 0 0.2 0 0 0 0 0 0.2 0 0 0 0 0 1 0" />
	      <feGaussianBlur result="blurOut" in="matrixOut" stdDeviation="10" />
	      <feBlend in="SourceGraphic" in2="blurOut" mode="normal" />
	    </filter>
	  </defs>
	  <rect width="90" height="90" stroke="green" stroke-width="3"
	  fill="yellow" filter="url(#f4)" />
	</svg>

**代码解释：**

* &lt;feColorMatrix&gt; 转换颜色，使偏移图片的颜色值接近空（0）
* 矩阵中三个'0.2'是获取了red、green、blue 通道