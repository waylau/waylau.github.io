---
layout: post
title:  Flex项目release后图片资源不见了–图片资源采用嵌入与不嵌入的区别
date:  2013-10-19 02:59
author: admin
comments: true
categories: [Flex]
tags: [Flex]
---

近期项目发现，在使用release发布项目包，图片找不到了，而debug下面又有图片。

后来发现，原来图片被另外一个组件嵌入了。

下面例子展示了组件嵌入和不嵌入图片的区别

```mxml
<?xml version=”1.0″ encoding=”utf-8″?> 
<s:Application xmlns:fx=”http://ns.adobe.com/mxml/2009″ xmlns:s=”library://ns.adobe.com/flex/spark” xmlns:mx=”library://ns.adobe.com/flex/mx” minWidth=”955″ minHeight=”600″> 
<fx:Declarations> 
<!– Flex项目release后图片资源不见了—-图片资源采用嵌入与不嵌入的区别 更多示例请访问www.waylau.com–> 
</fx:Declarations> 
<s:VGroup> 
<s:Panel title=”嵌入测试”> 
<s:HGroup> 
<s:Button id=”btn1″ icon=”@Embed(source=’images/close.png’)”/> 
<s:Button id=”btn2″ icon=”images/close.png”/> 
</s:HGroup> 
</s:Panel> 
<s:Panel title=”未嵌入测试”> 
<s:HGroup> <s:Button id=”btn3″ icon=”images/myIco.png”/> <s:Button id=”btn4″ icon=”images/myIco.png”/> </s:HGroup>
</s:Panel> 
</s:VGroup> 
</s:Application>
```

<img src="http://c.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=fbcd7cecd52a60595610e11f180f45a6/d8f9d72a6059252d7ee88d21369b033b5bb5b91c.jpg?referer=e1f409ba2cdda3cc52f38c1007f2&x=.jpg"/>

btn1嵌入了的图片，btn2就无法引用了， 而btn3、btn4没有嵌入，所以都可以引用。 看release的文件btn1嵌入的图片close.png是看不到的，已经嵌入进swf， 而myIco.png是存在的。
<img src="http://e.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=26458b6689d4b31cf43c94beb7ed5642/2f738bd4b31c8701cd2322c3257f9e2f0708ff7a.jpg?referer=2fbec8374d4a20a4680909f73918&x=.jpg"/>

嵌入图片和不嵌入图片各有利弊，嵌入图会加快访问图片的速度，但同时也增大了编译文件， 以下是一个错误的示例，多个文件嵌入了同一份资源save24.png，使得每个swf文件都变大了。要按照实际需要选择
<img src="http://f.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=1723011a49fbfbedd859367a48cb860b/a50f4bfbfbedab644ec3dde5f536afc379311e65.jpg?referer=4a848d2d1d30e92496b3a9015a3c&x=.jpg"/>

在上面的例子中当多个控件引用的是同一个资源时，嵌入图片用简单的变量访问方式，在多个地方引用所嵌入的资源。 这是变量就代表资源，提高写代码的效率

```mxml
<?xml version=”1.0″ encoding=”utf-8″?> 
<s:Application xmlns:fx=”http://ns.adobe.com/mxml/2009″ xmlns:s=”library://ns.adobe.com/flex/spark” xmlns:mx=”library://ns.adobe.com/flex/mx” minWidth=”955″ minHeight=”600″> 
<fx:Declarations> 
<!– Flex项目release后图片资源不见了,图片资源采用嵌入与不嵌入的区别 更多示例请访问www.waylau.com–> 
</fx:Declarations> 
<fx:Script> 
<![CDATA[ [Embed(source="images/close.png")] [Bindable] public var imgCls:Class; ]]> 
</fx:Script> 
<s:VGroup> 
<s:Panel title=”嵌入测试”> 
<s:HGroup> 
<s:Button id=”btn1″ icon=”{imgCls}”/> 
<s:Button id=”btn2″ icon=”{imgCls}”/> 
</s:HGroup> 
</s:Panel> 
<s:Panel title=”未嵌入测试”> 
<s:HGroup> 
<s:Button id=”btn3″ icon=”images/myIco.png”/> 
<s:Button id=”btn4″ icon=”images/myIco.png”/> 
</s:HGroup> 
</s:Panel> 
</s:VGroup> 
</s:Application>
```