---
layout: post
title: SVG &lt;marker&gt;创建箭头
date: 2014-05-29 07:08
author: admin
comments: true
categories: [SVG]
tags: [SVG,marker,arrow]
---
 
SVG 中&lt;line&gt;并没有箭头，可以通过&lt;marker&gt;进行扩展。

先定义一个箭头

    <defs>
        <marker id="markerArrow" markerWidth="13" markerHeight="13" refx="2" refy="6" orient="auto">
            <path d="M2,2 L2,11 L10,6 L2,2" style="fill: #000000;" />
        </marker>
    </defs>

其中`orient="auto"`设置箭头的方向为自动适应线条的方向。

而后，画line ,line的`marker-end`引用上面定义好的`markerArrow`即可
	
	<line x1="0" y1="0" x2="100" y2="50"  stroke="red" stroke-width="1" marker-end="url(#markerArrow)"  />

也可以应用在path中，
效果如下：

<svg width="500" height="100">
<defs>
<marker id="markerCircle" markerWidth="8" markerHeight="8" refx="5" refy="5">
<circle cx="5" cy="5" r="3" style="stroke: none; fill:#000000;"/>
</marker>
<marker id="markerArrow" markerWidth="13" markerHeight="13" refx="2" refy="6" orient="auto">
<path d="M2,2 L2,11 L10,6 L2,2" style="fill: #000000;" />
</marker>
</defs>
<line x1="0" y1="0" x2="100" y2="50"  stroke="red" stroke-width="1" marker-end="url(#markerArrow)"  />
<path d="M100,10 L150,10 L150,60"
style="stroke: #6666ff; stroke-width: 1px; fill: none;
marker-start: url(#markerCircle);
marker-mid:url(#arrow);
marker-end: url(#markerArrow) "
/>
</svg>

 
完整代码如下：
	
	<svg width="500" height="100">
    <defs>
        <marker id="markerCircle" markerWidth="8" markerHeight="8" refx="5" refy="5">
            <circle cx="5" cy="5" r="3" style="stroke: none; fill:#000000;"/>
        </marker>

        <marker id="markerArrow" markerWidth="13" markerHeight="13" refx="2" refy="6" orient="auto">
            <path d="M2,2 L2,11 L10,6 L2,2" style="fill: #000000;" />
        </marker>
    </defs>
    <line x1="0" y1="0" x2="100" y2="50"  stroke="red" stroke-width="1" marker-end="url(#markerArrow)"  />
    <path d="M100,10 L150,10 L150,60"
          style="stroke: #6666ff; stroke-width: 1px; fill: none;
                       marker-start: url(#markerCircle);
                       marker-mid:url(#arrow);
                       marker-end: url(#markerArrow) "
            />
	</svg>
