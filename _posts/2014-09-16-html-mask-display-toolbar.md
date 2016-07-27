---
layout: post
title: HTML 遮罩显示工具栏 
date: 2014-09-16 01:40
author: admin
comments: true
categories: [HTML]
tags: [HTML,mask]
---

#需求
想要实现一个照片，鼠标移到照片上，可以显示编辑该照片的工具栏

#实现
##整体布局
	  <div id="photo_id" class="photo_contarner" onmouseover="handlerIn( event )" onmouseout="handlerOut( event )">
	        <a href="javascript:void(0);" onclick="alert('我是底图');" >
	            <img  src="img/wl_white.png"  />
	            <div class="toolbar" name="toolbar" id="toolbar">
	                <a href="javascript:void(0);" class="toolbar_edit" onclick="alert('编辑一把');"  >
	                </a>
	                <a href="javascript:void(0);" class="toolbar_delete" onclick="alert('删了吧');" >
	                </a>
	            </div>
	        </a>
	    </div>

一个照片，上面加一个 div 工具栏（toolbar）里面有两个按钮

##样式

	.photo_contarner {
	    width: 128px;height: 128px;
	    margin: 20px;
	    float: left;
	    position:relative;
	}
	
	.photo_contarner img{
	    width: 128px;height: 128px;
	    border: 0
	}
	.toolbar {
	    position:absolute;
	    width: 48px;
	    height: 24px;
	    top: 0px;
	    right: 0px;
	    z-index:3;
	    background-color: gray;
	}
	.toolbar_edit {
	    background:url(../img/edit_24.png);
	}
	.toolbar_delete {
	    background:url(../img/remove_24.png);
	}
	.toolbar_edit,.toolbar_delete{
	    width:24px;height:24px;float: left;
	}

<a href="http://s1288.photobucket.com/user/waylau/media/waylau%20blog/bar002_zps5a21d9df.jpg.html" target="_blank"><img src="http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/bar002_zps5a21d9df.jpg" border="0" alt=" photo bar002_zps5a21d9df.jpg"/></a>

先看下整体效果，工具栏在照片上的右上角，工具栏包含两个图标。关键点： `z-index:3` 该属性设置元素的堆叠顺序。拥有更高堆叠顺序的元素总是会处于堆叠顺序较低的元素的前面。

默认工具栏为隐藏 .toolbar 的样式中添加

    display:none;

##事件
鼠标移入移出事件，移入为显示，移出为隐藏

var handlerIn = function( event ){
    document.getElementById('toolbar').style.display ='block';
};

var handlerOut = function( event ){
    document.getElementById('toolbar').style.display ='none';
};

移入
<a href="http://s1288.photobucket.com/user/waylau/media/waylau%20blog/bar002_zps5a21d9df.jpg.html" target="_blank"><img src="http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/bar002_zps5a21d9df.jpg" border="0" alt=" photo bar002_zps5a21d9df.jpg"/></a>

移出
<a href="http://s1288.photobucket.com/user/waylau/media/waylau%20blog/bar001_zps018d0a02.jpg.html" target="_blank"><img src="http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/bar001_zps018d0a02.jpg" border="0" alt=" photo bar001_zps018d0a02.jpg"/></a>

点击工具栏
<a href="http://s1288.photobucket.com/user/waylau/media/waylau%20blog/bar003_zpsf0a8f537.jpg.html" target="_blank"><img src="http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/bar003_zpsf0a8f537.jpg" border="0" alt=" photo bar003_zpsf0a8f537.jpg"/></a>

#源码
HTML 遮罩显示工具栏[源码](https://github.com/waylau/html-css-demo/blob/master/MaskDisplay.html)
