---
layout: post
title: Flex 4.6 自定义Datagrid添加checkbox\图片等功能
date: 2012-03-28 00:50
author: admin
comments: true
categories: [Flex]
tags: [Flex]
---
flex 4.6 中的 spark 的Datagrid相比于之前的 mx 有很大不同

实现自定义&lt;s:itemRenderer&gt;详见代码~

<img class="alignnone" alt="" src="http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=f08c7a529d3df8dca23d8f94fd2a03b6/3ac79f3df8dcd1003ae28c84708b4710b9122f1c.jpg?referer=8f670332db33c895ff69ac4b89f2&amp;x=.jpg" width="550" height="91" />
 
	<s:DataGrid id="dg" left="14" right="10" top="35" bottom="9" dataProvider="{datagrid_data}">
	<s:columns>
	<s:ArrayList>
	<s:GridColumn headerText="程序名称"
	dataField="name">
	<s:itemRenderer>
	<fx:Component>
	<s:GridItemRenderer>
	<s:HGroup verticalAlign="middle">
	<s:CheckBox id="cbx"/>
	<s:BitmapImage width="24" height="24" source="{data.pic}"/>
	<s:Label text="{data.name}"/>
	</s:HGroup>
	</s:GridItemRenderer>
	</fx:Component>
	</s:itemRenderer>
	</s:GridColumn>
	<s:GridColumn dataField="date" headerText="更新时间"/>
	<s:GridColumn dataField="version" headerText="版本号"/>
	<s:GridColumn dataField="moduleUrl" headerText="moduleUrl"/>
	<s:GridColumn />
	</s:ArrayList>
	</s:columns>
	</s:DataGrid>