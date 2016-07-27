---
layout: post
title: 纯AS3项目导入fl包的方法以及fl.controls.DataGrid的详细应用
date: 2013-07-05 02:00
author: admin
comments: true
categories: [ActionScript3]
---
在Flash CS4中打开“D:\Program Files\Adobe\Adobe Flash CS4\Common\Configuration\Components\”目录下的“User Interface.fla”文件
进入文件---发布设置---Flash---勾选“导出SWC”
点“导出影片”，输入文件名，确定之后就出现了swc文件
进入Flash builder，在ActionScript项目属性中引用刚刚导出的swc文件
这时就可以在源代码中 import fl.controls.* 并使用Flash CS4中的UI组件了

另外fl.controls.DataGrid的详细应用

参见：<a title="Flash datagrid component tutorial" href="http://edutechwiki.unige.ch/en/Flash_datagrid_component_tutorial">http://edutechwiki.unige.ch/en/Flash_datagrid_component_tutorial</a>

<a title="checkbox in datagrid flash as3中把checkbox组件放入datagrid中" href="http://crazyrabbit.blogbus.com/logs/38711416.html">http://crazyrabbit.blogbus.com/logs/38711416.html</a>
