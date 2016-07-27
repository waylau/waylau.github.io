---
layout: post
title: SVG Filters之虚化Blur Effects
date: 2014-05-04 14:08
author: admin
comments: true
categories: [Svg]
tags: [Svg,Filter]
---
##SVG 滤镜
在 SVG 中，可用的滤镜有：

* feBlend
* feColorMatrix
* feComponentTransfer
* feComposite
* feConvolveMatrix
* feDiffuseLighting
* feDisplacementMap
* feFlood
* feGaussianBlur
* feImage
* feMerge
* feMorphology
* feOffset
* feSpecularLighting
* feTile
* feTurbulence
* feDistantLight
* fePointLight
* feSpotLight

**注意：**

* 您可以在每个 SVG 元素上使用多个滤镜！
* IE 、Safari浏览器暂不支持滤镜!

##虚化Blur Effects;
&lt;filter&gt;标签用来定义 SVG 滤镜。&lt;filter&gt;标签使用必需的 id 属性来定义向图形应用哪个滤镜？
&lt;filter&gt;标签必须嵌套在 &lt;defs&gt; 标签内。&lt;defs&gt; 标签是 definitions 的缩写，它允许对诸如滤镜等特殊元素进行定义。

###SVG &lt;feGaussianBlur&gt;

####示例1

<svg height="110" width="110">
<defs>
<filter id="f1" x="0" y="0">
  <feGaussianBlur in="SourceGraphic" stdDeviation="15" />
</filter>
</defs>
<rect width="90" height="90" stroke="green" stroke-width="3"
fill="yellow" filter="url(#f1)" />
</svg>

	<svg height="110" width="110">
	  <defs>
	    <filter id="f1" x="0" y="0">
	      <feGaussianBlur in="SourceGraphic" stdDeviation="15" />
	    </filter>
	  </defs>
	  <rect width="90" height="90" stroke="green" stroke-width="3"
	  fill="yellow" filter="url(#f1)" />
	</svg>

**代码解释：**

* 该<filter>元素的id属性定义了滤镜的唯一名称
* <feGaussianBlur>元素定义了虚化效果
* 在in=“SourceGraphic”部分定义了在整个元素中创建的效果
* 该stdDeviation属性定义虚化的值
* 该<rect>元件的滤镜属性链接到了“f1”滤镜