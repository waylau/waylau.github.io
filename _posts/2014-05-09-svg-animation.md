---
layout: post
title: SVG Animation动画
date: 2014-05-09 07:08
author: admin
comments: true
categories: [Svg]
tags: [Svg,animation]
---
#SVG动画示例
下面是一个简单的SVG动画的例子：

<svg width="500" height="100">
<rect x="10" y="10" height="110" width="110"
   style="stroke:#ff0000; fill: #0000ff">
   <animateTransform
attributeName="transform"
begin="0s"
dur="20s"
type="rotate"
from="0 60 60"
to="360 60 60"
repeatCount="indefinite" 
   />
</rect>
</svg>

	<svg width="500" height="100">
	     <rect x="10" y="10" height="110" width="110"
	         style="stroke:#ff0000; fill: #0000ff">
	        <animateTransform
	            attributeName="transform"
	            begin="0s"
	            dur="20s"
	            type="rotate"
	            from="0 60 60"
	            to="360 60 60"
	            repeatCount="indefinite" 
	        />
	    </rect>
	</svg>

注意到，&lt;rect&gt;元素有一个&lt;animateTransform&gt;嵌套在它里面。正是这种元素驱动了矩形。
 

#动画选项的概述

动画是通过操纵图形随时间变化的属性而进行的。用下面5个SVG动画元素的
一个或多个来完成的：

* &lt;set&gt;
* &lt;animate&gt;
* &lt;animateColor&gt;
* &lt;animateTransform&gt;
* &lt;animateMotion&gt;
 
这些动画元素在整个这段文字的后面要谈到的。

##set

&lt;set&gt;元素是最简单的SVG动画元素。j简单地将一个属性修改成一定值后，特定时间间隔过后就能能效了。因此，该图形是不连续的动画，只是改变属性值一次。

这里是一个&lt;set&gt;元素的例子：

<svg width="500" height="100">
<circle cx="30" cy="30" r="25" style="stroke: none; fill: #0000ff;">
<set attributeName="r" attributeType="XML"
 to="100"
 begin="5s" />
</circle>
</svg>

	<svg width="500" height="100">
	    <circle cx="30" cy="30" r="25" style="stroke: none; fill: #0000ff;">
	        <set attributeName="r" attributeType="XML"
	                 to="100"
	                 begin="5s" />
	    </circle>
	</svg>


注意&lt;set&gt;嵌套在*circle*里面。这就是&lt;set&gt;元素的用法。

该&lt;set&gt;元素将时间设置成一个属性的值。设置属性的名称是指定的*attributeName*。修改成的值为*to*。开始生效的时间间隔为*begin*。

上面的例子中的属性设置*r* 5秒后为100。


##attributeType

在前面的例子也有一个*attributeType*属性在&lt;set&gt;元素里面。该值设置为*XML*。因为SVG的&lt;circle &gt;元素素是XML元素。

也可以将*attributeType*属性设置为*CSS*。

<svg width="400" height="400">
<rect x="20" y="20" width="250" height="250" style="fill:blue">
<animate attributeType="CSS" attributeName="opacity" from="1" to="0" dur="5s" repeatCount="indefinite" />
</rect>
Sorry, your browser does not support inline SVG.  
</svg>

	<svg width="400" height="400">
	  <rect x="20" y="20" width="250" height="250" style="fill:blue">
	    <animate attributeType="CSS" attributeName="opacity" from="1" to="0" dur="5s" repeatCount="indefinite" />
	  </rect>
	  Sorry, your browser does not support inline SVG.  
	</svg>

如果你不提供一个*attributeType*属性，那么浏览器会尝试猜测是一个*XML*属性还是*CSS*属性。

##animate

该*animate*元素被用来驱动SVG图形的属性。下面是一个例子：

<svg width="500" height="75">
<circle cx="30" cy="30" r="25" style="stroke: none; fill: #0000ff;">
<animate attributeName="cx" attributeType="XML"
 from="30"  to="470"
 begin="0s" dur="5s"
 fill="remove" repeatCount="indefinite"/>
</circle>
</svg>
	
	<svg width="500" height="75">
	    <circle cx="30" cy="30" r="25" style="stroke: none; fill: #0000ff;">
	        <animate attributeName="cx" attributeType="XML"
	                 from="30"  to="470"
	                 begin="0s" dur="5s"
	                 fill="remove" repeatCount="indefinite"/>
	    </circle>
	</svg>


这个例子中的&lt;circle&gt;元素*cx*的属性从30改成了479。动画从0秒开始，并具有5秒持续时间。

当动画完成，*animate*的属性被设置回其原始值（*fill="remove"*）。如果想要的将动画属保持在*to*值的位置，则*fill*设置为*"freeze"*。动画如果无限重复则设置*repeatCount*的值。

 
##animateColor

<svg width="500" height="80">
<circle cx="30" cy="30" r="25" style="stroke: none; fill: #0000ff;">
<animateColor attributeName="fill"
 from="#0000ff"  to="#ff0000"
 begin="0s" dur="5s"
 fill="freeze" repeatCount="indefinite"/>
</circle>
</svg>

	<svg width="500" height="80">
	    <circle cx="30" cy="30" r="25" style="stroke: none; fill: #0000ff;">
	        <animateColor attributeName="fill"
	                 from="#0000ff"  to="#ff0000"
	                 begin="0s" dur="5s"
	                 fill="freeze" repeatCount="indefinite"/>
	    </circle>
	</svg>


这个例子中的动画填写将CSS属性从颜色*＃0000FF*（蓝色）的颜色转为*＃FF0000*（红色）。
 
**注**：
尽管SVG定义了"animateColor"，但是它已经被弃用了，替代它的是"animate"元素。


##animateTransform

&lt;animateTransform&gt;元素可以驱动图形的*transform&*的属性。而&lt;animate&gt;元素不能做到这一点。
下面是一个例子：

<svg width="500" height="200">
<rect x="20" y="20" width="100" height="40"
style="stroke: #ff00ff; fill: none;" >
<animateTransform attributeName="transform"
type="rotate"
from="0 100 100" to="360 100 100"
begin="0s" dur="10s"
repeatCount="indefinite"
/>
</rect>
<circle cx="100" cy="100" r="2" style="stroke: none; fill: #0000ff;"/>
</svg>

	<svg width="500" height="200">
	
	  <rect x="20" y="20" width="100" height="40"
	        style="stroke: #ff00ff; fill: none;" >
	      <animateTransform attributeName="transform"
	                        type="rotate"
	                        from="0 100 100" to="360 100 100"
	                        begin="0s" dur="10s"
	                        repeatCount="indefinite"
	              />
	  </rect>
	  <circle cx="100" cy="100" r="2" style="stroke: none; fill: #0000ff;"/>
	</svg>


本例中在点（100,100）位置从0至360度进行旋绕。


下面是比例尺放大的示例：

<svg width="500" height="200">
<rect x="20" y="20" width="40" height="40"
style="stroke: #ff00ff; fill: none;" >
<animateTransform attributeName="transform"
  type="scale"
  from="1 1" to="2 3"
  begin="0s" dur="10s"
  repeatCount="indefinite"
/>
</rect>
</svg>

	<svg width="500" height="200">
	
	    <rect x="20" y="20" width="40" height="40"
	          style="stroke: #ff00ff; fill: none;" >
	        <animateTransform attributeName="transform"
	                          type="scale"
	                          from="1 1" to="2 3"
	                          begin="0s" dur="10s"
	                          repeatCount="indefinite"
	                />
	    </rect>
	</svg>

 
##animateMotion

该&lt;animateMotion&gt;元素可以驱动图形沿着一条路径运动。下面是一个例子：

<svg width="500" height="150">
<path d="M10,50 q60,50 100,0 q60,-50 100,0"
style="stroke: #000000; fill: none;"
  />
<rect x="0" y="0" width="30" height="15"
  style="stroke: #ff0000; fill: none;">
<animateMotion
  path="M10,50 q60,50 100,0 q60,-50 100,0"
  begin="0s" dur="10s" repeatCount="indefinite"
  />
</rect>
</svg>

	<svg width="500" height="150">
	  <path d="M10,50 q60,50 100,0 q60,-50 100,0"
	    style="stroke: #000000; fill: none;"
	          />
	  <rect x="0" y="0" width="30" height="15"
	          style="stroke: #ff0000; fill: none;">
	      <animateMotion
	          path="M10,50 q60,50 100,0 q60,-50 100,0"
	          begin="0s" dur="10s" repeatCount="indefinite"
	          />
	  </rect>
	</svg>

为了以配合道路的坡度，设置了*rotate="auto"*。下面是一个例子：

<svg width="500" height="150">
<path d="M10,50 q60,50 100,0 q60,-50 100,0"
style="stroke: #000000; fill: none;"
/>
<rect x="0" y="0" width="30" height="15"
style="stroke: #ff0000; fill: none;">
<animateMotion
path="M10,50 q60,50 100,0 q60,-50 100,0"
begin="0s" dur="10s" repeatCount="indefinite"
rotate="auto"
/>
</rect>
</svg>

	<svg width="500" height="150">
	
	    <path d="M10,50 q60,50 100,0 q60,-50 100,0"
	          style="stroke: #000000; fill: none;"
	            />
	
	    <rect x="0" y="0" width="30" height="15"
	          style="stroke: #ff0000; fill: none;">
	        <animateMotion
	                path="M10,50 q60,50 100,0 q60,-50 100,0"
	                begin="0s" dur="10s" repeatCount="indefinite"
	                rotate="auto"
	                />
	    </rect>
	</svg>

##Time Units（时间单位）

时间单位经常用在*begin*,*dur*和*end*的属性值中，*"5s"*表示"5秒"。
 
| 单位   |  描述  |
| -------| :----:  |
| h   |   Hours（时） |
| min |   Minutes(分)|
| s  |  Seconds(秒) |
| ms  |  Milliseconds(毫秒)  |

也可以是这种`hh:mm:ss`这种格式，比如`1:30:45`表示时间长度为"1小时30分钟45秒"。
    

##Coordinating Animations（协调动画）

<svg width="500" height="100">
<rect x="0" y="0" width="30" height="15"
style="stroke: #ff0000; fill: none;">
<animate id="one"
attributeName="x" attributeType="XML"
from="0" to="400"
begin="0s" dur="10s" fill="freeze"
/>
<animate
attributeName="y" attributeType="XML"
from="0" to="80"
begin="one.end" dur="10s" fill="freeze"
/>
</rect>
</svg>

	<svg width="500" height="100">
		
		<rect x="0" y="0" width="30" height="15"
		      style="stroke: #ff0000; fill: none;">
		
		    <animate id="one"
		            attributeName="x" attributeType="XML"
		            from="0" to="400"
		            begin="0s" dur="10s" fill="freeze"
		            />
		    <animate
		            attributeName="y" attributeType="XML"
		            from="0" to="80"
		            begin="one.end" dur="10s" fill="freeze"
		            />
		</rect>
	
	</svg>
一个动画的开始时间是另外一个动画的结束时间。

也可以通过设置时间的偏移量
	one.begin+10s
	one.end+5s

 
另外，你可以在一个动画指​​定一个明确的结束时间*end*属性。这并不能取代*dur*属性。它所做的是添加另一种可能结束一个动画，所以以先到为准。下面是一个例子：

	<animate
    attributeName="y" attributeType="XML"
    from="0" to="50"
    begin="one.begin+3s" dur="10s" end="one.end"
    fill="freeze"
    />

该动画将有10秒的持续时间，或与*one*动画一起停止结束，以先到那个为准。


##Repeating Animations（重复动画）

重复动画可以用动画元素中的两个属性。
* 第一个属性是的*repeatCount*属性。在这个属性，你可以设置一个数值，这是重复动画的次数，或值不确定，将继续运行动画不会停下。
* 第二属性是*repeatDur*其中指定该动画将被重复的持续时间。 

<svg width="500"  height="100">
<rect x="10" y="10" width="40" height="20"
style="stroke: #000000; fill: none;">
<animate attributeName="x" attributeType="XML"
 from="10" to="400"
 begin="0s" dur="10s"
 repeatCount="indefinite"
/>
<animate attributeName="y" attributeType="XML"
 from="10" to="100"
 begin="0s" dur="10s"
 fill="freeze"
 repeatCount="indefinite"
/>
</rect>
</svg>

	<svg width="500"  height="100">
	   <rect x="10" y="10" width="40" height="20"
	         style="stroke: #000000; fill: none;">
	        <animate attributeName="x" attributeType="XML"
	                 from="10" to="400"
	                 begin="0s" dur="10s"
	                 repeatCount="indefinite"
	                />
	        <animate attributeName="y" attributeType="XML"
	                 from="10" to="100"
	                 begin="0s" dur="10s"
	                 fill="freeze"
	                 repeatCount="indefinite"
	                />
	   </rect>
	</svg>


##Combining Animations（结合动画）

动画可以组合起来用，这里是一个例子：

<svg width="500"  height="100">
<rect x="10" y="10" width="40" height="20"
style="stroke: #000000; fill: none;">
<animate attributeName="x" attributeType="XML"
 from="10" to="400"
 begin="0s" dur="10s"
 repeatCount="indefinite"
/>
<animate attributeName="y" attributeType="XML"
 from="10" to="100"
 begin="0s" dur="10s"
 fill="freeze"
 repeatCount="indefinite"
/>
</rect>
</svg>
	
	<svg width="500"  height="100">
	   <rect x="10" y="10" width="40" height="20"
	         style="stroke: #000000; fill: none;">
	        <animate attributeName="x" attributeType="XML"
	                 from="10" to="400"
	                 begin="0s" dur="10s"
	                 repeatCount="indefinite"
	                />
	        <animate attributeName="y" attributeType="XML"
	                 from="10" to="100"
	                 begin="0s" dur="10s"
	                 fill="freeze"
	                 repeatCount="indefinite"
	                />
	   </rect>
	</svg>

这个例子中有两个动画，每个动画的分别驱动*x*和*Ÿ*的属性。

当组合&lt;animateTransform&gt;元素，默认的行为是第二个动画来抵消第一个。但是，您可以通过设置*additive*属性值为*sum*的而使两种动画效果累加。

<svg width="500"  height="100">
<rect x="10" y="10" width="40" height="20"
style="stroke: #000000; fill: none;">
<animateTransform attributeName="transform" attributeType="XML"
 type="scale"
 from="1" to="3"
 begin="0s" dur="10s"
 repeatCount="indefinite"
 additive="sum"
/>
<animateTransform attributeName="transform" attributeType="XML"
 type="rotate"
 from="0 30 20" to="360 30 20"
 begin="0s" dur="10s"
 fill="freeze"
 repeatCount="indefinite"
 additive="sum"
/>
</rect>
</svg>

	<svg width="500"  height="100">
	    <rect x="10" y="10" width="40" height="20"
	          style="stroke: #000000; fill: none;">
	        <animateTransform attributeName="transform" attributeType="XML"
	                 type="scale"
	                 from="1" to="3"
	                 begin="0s" dur="10s"
	                 repeatCount="indefinite"
	                 additive="sum"
	                />
	        <animateTransform attributeName="transform" attributeType="XML"
	                 type="rotate"
	                 from="0 30 20" to="360 30 20"
	                 begin="0s" dur="10s"
	                 fill="freeze"
	                 repeatCount="indefinite"
	                 additive="sum"
	                />
	    </rect>
	</svg>



参考：<http://tutorials.jenkov.com/svg/svg-animation.html>