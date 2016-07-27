---
layout: post
title: Flex/AS3/flash player支持屏蔽右键菜单,自定义菜单，并设置相应的菜单事件(示例，图解)
date: 2013-03-04 00:45
author: admin
comments: true
categories: [Flash,Flex]
tags: [Flash, Flex, 右键]
---
Flex/AS3/flash player支持屏蔽右键菜单,自定义菜单，并设置相应的菜单事件(示例，图解)

播放器 版本 11.2以后支持右键菜单屏蔽及自定义菜单
1.更新播放器 ，11.2 以上版本

	http://download.macromedia.com/get/flashplayer/updaters/11/playerglobal11_3.swc
	http://download.macromedia.com/get/flashplayer/updaters/11/playerglobal11_4.swc
	http://download.macromedia.com/get/flashplayer/updaters/11/playerglobal11_2.swc
	http://download.macromedia.com/get/flashplayer/updaters/11/playerglobal11_5.swc

2.编译器参数设置-swf-version=15（见图）

<img class="alignnone" alt="" src="http://c.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=add4d17a74c6a7efbd26a823cdc1de6c/91ef76c6a7efce1bd7d3bc6fad51f3deb58f659e.jpg?referer=ecdbe946ff1f4134b920314ec374&amp;x=.jpg" width="280" height="115" />

3. 自定义 菜单，并设置相应的菜单事件 (Flex代码)
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;s:Application xmlns:fx="http://ns.adobe.com/mxml/2009"
			   xmlns:s="library://ns.adobe.com/flex/spark"
			   xmlns:mx="library://ns.adobe.com/flex/mx"
			   xmlns:twaver="http://www.servasoftware.com/2009/twaver/flex"
			   applicationComplete="init()"&gt;
	&lt;fx:Script&gt;
		&lt;![CDATA[

			import mx.controls.Button;
			import mx.controls.Menu;
			import mx.events.MenuEvent;
			import mx.controls.Alert;

			private var menu:Menu = null;
			private var bt:Button = new Button();

			private function init():void {
				bt.label = "走你~";
				network.addElement(bt);
				network.addEventListener(MouseEvent.RIGHT_CLICK,handleRighClick);//监听右键
			}
			private function handleRighClick(e:MouseEvent):void {

				if(menu != null){
					menu.hide();
				}
				menu = Menu.createMenu(network, myMenuData, false);//myMenuData菜单数据源
				menu.labelField = "@label";//右键菜单的文字

				var point:Point = network.globalToLocal(new Point(e.stageX, e.stageY));
				menu.show(point.x, point.y);
				menu.addEventListener(MenuEvent.ITEM_CLICK,menuItemClickHandler);  //菜单事件
			}

			private function menuItemClickHandler(evt:MenuEvent):void{
				Alert.show(evt.item.@eventName)  //将事件名显示出来
			}
		]]&gt;
	&lt;/fx:Script&gt;
	&lt;fx:Declarations&gt;
		&lt;fx:XML id="myMenuData"&gt;
			&lt;root label="MenuItem 1" &gt;
				&lt;menuitem label="new" eventName="new" /&gt;
				&lt;menuitem label="refreash" eventName="refreash" /&gt;
				&lt;menuitem label="remove" eventName="remove" /&gt;
			&lt;/root&gt;

		&lt;/fx:XML&gt;
	&lt;/fx:Declarations&gt;
	&lt;s:Group id="network" width="100%" height="100%"  /&gt;
&lt;/s:Application&gt;</pre>
<img class="alignnone" alt="" src="http://f.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=00d9908671f082022992913a7bc08ad0/d01373f082025aaf099d0a6ef9edab64024f1ae3.jpg?referer=0c19c643257f9e2f2922293866a2&amp;x=.jpg" width="211" height="129" />

<img class="alignnone" alt="" src="http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=8e695c00087b020808c93fe452e283ee/1c950a7b02087bf4eabe234cf0d3572c10dfcfc1.jpg?referer=164b2af50e338744c5d21b4c29df&amp;x=.jpg" width="290" height="179" />
