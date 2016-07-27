---
layout: post
title: SVG Viewport和View Box
date: 2014-06-03 07:08
author: admin
comments: true
categories: [SVG]
tags: [SVG,viewport,viewbox]
---
 
SVG Viewport和View Box用来设置图像的可见部分的尺寸。

##Viewport（视口）
Viewport：视口，视觉窗口，是SVG图像的可见区域。SVG图像逻辑上是能显示你想要的宽和高，但实际上同一时间仅图像的某一部分是可见的。该可见的区域被称为Viewport。

您可以通过&lt;svg&gt;元素的`width`和`height`指定使用的Viewport宽度和高度的下面是一个例子：

	<svg width="500" height="300"></svg>

这例子定义了一个Viewport，500个单位宽和300个单位高。

##坐标单位
如果你不指定任何内部宽度和高度属性的单位，单位都假定为像素。即，宽度500表示500个像素的宽度。

除了像素，还可以使用下面的单位：

单位 | 描述
----|-----
em	 |默认字体大小 ，通常是一个字符高度
ex	 |x字符的高度
px	 |像素
pt	 |点（一英寸的1/72）
pc	 |派卡（一英寸的1/6）
cm	 |厘米
mm	 |毫米
in	 |英寸

在&lt;svg&gt;元素里面设置单位只会影响的&lt;svg&gt;的大小（即Viewport）。在SVG图像中显示的SVG图形的大小由你对每个图形设置的单位决定。如果没有指定单位，各单位将默认为像素。

下面是一个例子：

	<svg width="10cm" height="10cm">
	    <rect x="50" y="100" width="50" height="50"
	          style="stroke: #000000; fill: none;"/>
	    <rect x="100" y="100" width="50mm" height="50mm"
	          style="stroke: #000000; fill: none;" />
	</svg>

该&lt;svg&gt;图像有其单位设置为厘米。两个&lt;rect&gt;元素都有自己的单位设置。一个使用像素（没有单位明确设定），另一个使用毫米。

这里是所得到的图象，右边的（单位毫米）大于左边（单位像素）。

<svg width="10cm" height="10cm">
<rect x="50" y="100" width="50" height="50"
  style="stroke: #000000; fill: none;"/>
<rect x="100" y="100" width="50mm" height="50mm"
  style="stroke: #000000; fill: none;" />
</svg>

##View Box
您可以重新在&lt;svg&gt;元素内部定义没有单位的坐标，通过`viewBox`属性来实现。下面是一个例子：


	<svg width="500" height="200" viewBox="0 0 50 20" style="border: 1px solid #234567;">
	    <rect x="20" y="10" width="10" height="5"
	          style="stroke: #000000; fill:none;"/>
	</svg>

此示例创建一个&lt;svg&gt;为500像素宽和200像素高，宽度元素的的`viewBox`属性包含四个坐标。这些坐标就定义了&lt;svg&gt;的`viewBox`属性。

在这种情况下，View Box跨度从0,0到50,20。这意味着，500 x 200像素 &lt;svg&gt;元素内部使用一个坐标系，它从0,0 到50,20。换言之，图形中使用的坐标，每1个单位，在&lt;svg&gt;对应于宽度500/50 = 10个像素，而在高200/20 = 10个像素。这就是为什么x轴为20及y轴为10的位置的矩形是真正位于（200,100），其宽度（10）和高度（5）对应于100个像素和50个像素。

另一种方式来解释它，`viewBox`属性的前两个坐标定义的是&lt;svg&gt;左上角的用户坐标，后两个坐标定义的是&lt;svg&gt;右下角的用户坐标。该&lt;svg&gt;内部的空间被解释为从左上角坐标到右下角坐标跨越。

这里是所得到的图象：


<svg width="500" height="200" viewBox="0 0 50 20" style="border: 1px solid #234567;">
<rect x="20" y="10" width="10" height="5"
  style="stroke: #000000; fill:none;"/>
</svg>

注意&lt;rect&gt;里面的所有坐标元素1个单位就代表10像素。

##preserveAspectRatio保持宽高比
如果Viewport和View Box不具有相同的宽高比（宽度与高度之比），您需要指定SVG显示器（例如浏览器）如何显示SVG图像。使用&lt;svg&gt;的preserveAspectRatio属性。

该preserveAspectRatio属性需要用空格分隔的两个值。第一个值将告诉您View Box如何在Viewport中对齐。这个值本身就是两部分组成。第二个值将告诉您宽高比如何将被保留（如果有的话）。

第一个值指定的对齐由两部分组成。第一部分指定的x对齐和第二部分指定的y对齐。这里是x轴和y取向的值的列表：

值	|描述
----|-----
xMin	|对齐View Box最小x轴的值与Viewport的左边缘。
xMid	|对齐View Box的x轴的中心与Viewport的中心x轴坐标。
xMax	|对齐View Box最大x轴的值与Viewport的右边缘。
YMin	|对齐View Box最小y轴的值与Viewport的顶部边缘。
YMid	|对齐View Box的y轴的中心与Viewport的中心y轴坐标。
YMin	|对齐View Box最大y轴的值与Viewport的底部边缘。

这里有两个例子：

	xMaxYMax

	xMidYMid

 
在preserveAspectRatio第二部分属性值可以采取三种不同的值：

值	|描述
----|-----
meet |	保持宽高比和比例视图框，以适应内视口。
slice|  保留宽高比，任何不适合Viewport宽高比的图像的会被切掉。
none |	不保留宽高比。缩放图像以View Box充满完全适应Viewport。比例会被扭曲。

在第二部分preserveAspectRatio属性值附加到第一部分，以空格分隔。这里有两个例子：

	preserveAspectRatio="xMidYMid meet"
	
	preserveAspectRatio="xMinYMin slice"
 
下面的实施例都具有宽度为500，高度为75和的viewBox属性设为0 0 250 75。这意味着，沿x轴的每个坐标单元将对应于2个像素，而是沿着Y轴的每个坐标单位只对应1个像素。在x轴缩放比为500/250=2 ，y轴的缩放比为75/75=1。这可能会导致一个扭曲，但我们会在下面的例子查看如何设置各种preserveAspectRatio。

<iframe style="width: 100%; height: 300px" src="http://www.w3.org/TR/SVG11/images/coords/PreserveAspectRatio.svg" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

[查看例子](http://www.w3.org/TR/SVG11/images/coords/PreserveAspectRatio.svg)

 