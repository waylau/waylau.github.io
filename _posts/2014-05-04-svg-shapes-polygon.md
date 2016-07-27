---
layout: post
title: SVG Shapes之多边形 &lt;polygon&gt;
date: 2014-05-04 12:06
author: admin
comments: true
categories: [Svg]
tags: [Svg,shape,polygon]
---

##5.多边形 &lt;polygon&gt;

####示例5.1
<svg height="210" width="500">
  <polygon points="200,10 250,190 160,210" style="fill:lime;stroke:purple;stroke-width:1" />
</svg>

	<svg height="210" width="500">
	  <polygon points="200,10 250,190 160,210" style="fill:lime;stroke:purple;stroke-width:1" />
	</svg>
**代码解释：**

* points 属性定义多边形每个角的 x 和 y 坐标

####示例5.2
<svg height="250" width="500">
  <polygon points="220,10 300,210 170,250 123,234" style="fill:lime;stroke:purple;stroke-width:1" />
</svg>

	<svg height="250" width="500">
	  <polygon points="220,10 300,210 170,250 123,234" style="fill:lime;stroke:purple;stroke-width:1" />
	</svg>

####示例5.3
<svg height="210" width="500">
  <polygon points="100,10 40,180 190,60 10,60 160,180"
  style="fill:lime;stroke:purple;stroke-width:5;fill-rule:nonzero;" />
</svg>

	<svg height="210" width="500">
	  <polygon points="100,10 40,180 190,60 10,60 160,180"
	  style="fill:lime;stroke:purple;stroke-width:5;fill-rule:nonzero;" />
	</svg>

####示例5.4
fill-rule属性改为"evenodd"

<svg height="210" width="500">
  <polygon points="100,10 40,180 190,60 10,60 160,180"
  style="fill:lime;stroke:purple;stroke-width:5;fill-rule:evenodd;" />
</svg>

	<svg height="210" width="500">
	  <polygon points="100,10 40,180 190,60 10,60 160,180"
	  style="fill:lime;stroke:purple;stroke-width:5;fill-rule:evenodd;" />
	</svg>
 