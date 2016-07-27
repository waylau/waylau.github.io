---
layout: post
title: 在SVG中使用外部绘图
date: 2014-06-12 07:08
author: admin
comments: true
categories: [SVG]
tags: [SVG,image]
---
  
您可以在 SVG 文档中包括预定义的内容 —— 在呈现的文档内或文档之外定义的内容。

#引用内部绘图
symbol,defs 都可以事先定义好图形，而后use来引用


```svg
<svg  width="200" height="200" version="1.1"
        xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
    <symbol id="shape2">
        <circle cx="25" cy="25" r="25"></circle>
        <title>My image</title>
    </symbol>

    <defs>
        <g id="shape3">
            <circle cx="25" cy="25" r="25" fill="red"></circle>
        </g>
    </defs>
    <use xlink:href="#shape2" x="50" y="25" ></use>
    <use xlink:href="#shape3" x="150" y="25" ></use>
</svg>
```

<svg  width="200" height="200"  version="1.1"
xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<symbol id="shape2">
<circle cx="25" cy="25" r="25"></circle>
<title>My image</title>
</symbol>
<defs>
<g id="shape3">
   <circle cx="25" cy="25" r="25" fill="red"></circle>
</g>
</defs>
<use xlink:href="#shape2" x="50" y="25" ></use>
<use xlink:href="#shape3" x="150" y="25" ></use>
</svg>

有关symbol,defs的异同点，可以参考[这篇文章](http://www.html5zhan.com/jingyan/info/530577c72e05e83b801642dc)

#引用外部绘图
我们事先已经准备好了一个外部绘图svg文件[svg-file-test.svg](../assets/img/svg-file-test.svg)

##1.使用&lt;image&gt;
使用预定义内容作为 SVG 绘图一部分的方法之一是使用 &lt;image&gt;元素。在概念上，SVG 中的&lt;image&gt;与 HTML 中的 &lt;img&gt;非常类似：该元素只须指示呈现客户机在当前的 SVG 环境中绘制外部图像的内容 —— 其本身可能是 SVG 或者是 JPEG 或 PNG 格式的光栅图像。您几乎可以像调整一个规则的图形元素那样来调整外部图像的大小和改变其位置

```svg
<svg  width="100%" height="100%" version="1.1"
        xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
    <image  x="0" y="0" width="300" height="200"  xlink:href="../assets/img/svg-file-test.svg"></image>

    <image  x="0" y="200" width="300" height="200"  xlink:href="../assets/img/wl_white.png"></image>
</svg>
```

<svg   width="100%" height="100%" version="1.1"
xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<image  x="0" y="0" width="300" height="200"  xlink:href="../assets/img/svg-file-test.svg"></image>
<image  x="0" y="200" width="300" height="200"  xlink:href="../assets/img/wl_white.png"></image>
</svg>


##2.使用&lt;use&gt;元素
任何svg, symbol, g, 单个的图形元素和use元素本质上都可以作为模板对象被use元素引用(例如初始化)。use引用的图形内容会在指定的位置渲染。与image元素不同，use元素不能引用整个文档。

```svg
<svg    width="100%" height="100%" version="1.1"
        xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
    <use xlink:href="../assets/img/svg-file-test.svg#svg2" x="0" y="0"  width="100" height="100" ></use>
    <use xlink:href="../assets/img/svg-file-test.svg#rect2985" x="0" y="325"  width="300" height="200" ></use>
</svg>
```

<svg    width="100%" height="100%" version="1.1"
xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<use xlink:href="../assets/img/svg-file-test.svg#svg2" x="0" y="0"  width="100" height="100" ></use>
<use xlink:href="../assets/img/svg-file-test.svg#rect2985" x="0" y="325"  width="300" height="200" ></use>
</svg>


第一个use引用了该文件的整个svg元素。

第二个use引用了该文件的rect元素。


 