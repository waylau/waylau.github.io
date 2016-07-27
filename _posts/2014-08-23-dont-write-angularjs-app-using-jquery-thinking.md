---
layout: post
title: 不要用jQuery思维写AngularJS应用
date: 2014-08-23 01:40
author: admin
comments: true
categories: [AngularJS]
tags: [AngularJS,jQuery]
---
 
#jQuery and AngularJS 苹果、橙

jQuery 和 AngularJS 就像苹果和橙，本身不是一类东西，注重点也不同。

jQuery 主要是用来操作DOM，作用就是消除各浏览器的差异，简化和丰富DOM 的API，比如 DOM 文档的转换，事件处理，动画，和 AJAX 交互等。

AngularJS 是一个完整的框架，试图解决现代 Web 2.0 应用程序开发的各个方面。 AngularJS 有着诸多特性，最为核心的是：MVC、模块化、自动化双向数据绑定、语义化标签、依赖注入，等等。(看到了Flex 和 Spring 的影子)

最重要的是，用 AngularJS 以一种完全不同的方法构建用户界面，其中以声明方式指定视图的模型驱动的变化。而 jQuery 常常需要编写以DOM为中心的代码，随着项目的增长（无论是在规模和交互性方面）将会变得越来越难控制。

用 AngularJS 以现代的Web应用程序开发的整体方法，并试图让浏览器更好的发展平台。

#简单的例子比较
几个例子看出他们的不同点

##模板

在jQuery里面，会用编程的方式来修改视图，像下面这样用`ul`标签来定义一个下拉列表：


	<ul class="main-menu">
	    <li class="active">
	        <a href="#/home">Home</a>
	    </li>
	    <li>
	        <a href="#/menu1">Menu 1</a>
	        <ul>
	            <li><a href="#/sm1">Submenu 1</a></li>
	            <li><a href="#/sm2">Submenu 2</a></li>
	            <li><a href="#/sm3">Submenu 3</a></li>
	        </ul>
	    </li>
	    <li>
	        <a href="#/home">Menu 2</a>
	    </li>
	</ul>

在jQuery里面，会像下面这行代码一样来创建这个下拉列表：
 
	$('.main-menu').dropdownMenu();
 
如果我们仅仅看视图代码，我们无法立刻发现它有什么功能。对于小型的应用来说，这样做还算可以。但是对于大型应用来说，很快就会变得混乱并难以维护。
 
然而，在AngularJS中，视图是一种模板，一种潜规则，`ul`声明看起来就像下面这样：
 
	<ul class="main-menu" dropdown-menu>
	    ...
	</ul>
 
两种方式的效果完全相同，但是在 AngularJS 的版本中，每一个看到模板的人都知道它在做什么（潜规则）。 不管什么时候开发团队有新人进来，她看到这种代码之后就会立即明白，存在一个叫做`dropdownMenu`的指令，这个指令负责操控这个视图。凭直觉就可以知道答案，没有必要看任何代码。视图本身已经告诉了我们这里会发生什么。这样就更加清晰了。

##数据绑定
数据绑定作为 AngularJS 的特性之一，更新视图，无需操纵 DOM ，剩下很多代码（ 想想 Flex ）。在jQuery更新视图的步骤是这样的，将设有如下视图

	<ul class="messages" id="log">
	</ul>

我要在`ul`插入`li`,需要操作 DOM 来添加节点元素。

	$.ajax({
	  url: '/myEndpoint.json',
	  success: function ( data, status ) {
	    $('ul#log').append('<li>Data Received!</li>');
	  }
	});

假设，此时，我要删除`log`视图，那我不得不去 操作 DOM,把ajax 里面的添加方法修改了。太蛋疼了。其实在 js 里面写 html 本身就是一件困难的事，因为 html 包含尖括号、属性、双引号、单引号、方法，在 js 需要对这些特殊符号进行转义 ，代码将会变得冗长易出错且难以识别。 如下面的例子：

	var str = "<a href=# name=link5 class="menu1 id=link1" + "onmouseover=MM_showMenu(window.mm_menu_0604091621_0,-10,20,null,\'link5\');"+ "sel1.style.display=\'none\';sel2.style.display=\'none\';sel3.style.display='none\';"+" onmouseout=MM_startTimeout();>Free Services</a> ";
	
	document.write(str);

看下 AngularJS 是怎么干的，先定义一个界面模板

	<ul class="messages">
	    <li ng-repeat="entry in log">{{ entry.msg }}</li>
	</ul>

而后更新数据

	$http( '/myEndpoint.json' ).then( function ( response ) {
	    $scope.log.push( { msg: 'Data Received!' } );
	});

视图就会根据数据自动刷新了。而界面改成什么样，并不需要去修改更新数据的接口，比如改成下面的样子

	<div class="messages">
	    <div class="alert" ng-repeat="entry in log">
	        {{ entry.msg }}
	    </div>
	</div>


#不要用jQuery思维写AngularJS应用
用 AngularJS 模型为中心和 jQuery DOM为中心的范式完全不同。经验丰富的jQuery开发者开发 AngularJS 可能会陷入“范式的陷阱”。建议你跳过 JQuery 的依赖，同时学习用 AngularJS（只是不想回到旧的习惯和学会用AngularJS方式解决问题）。不要混合使用 jQuery 和 AngularJS 。不要使用 jQuery 。最好不要引入它。当你遇到一个问题，而这个问题你知道如何使用 jQuery 去解决，那么在你使用$之前， 请思考一下如何以 AngularJS 的方式去解决它。 jQuery 从来就不是必须的。 

在架构上，AngularJS 主要是用来构架[SPA](http://n.wikipedia.org/wiki/Single-page_application)单页面应用，而不是普通的网页集合，所以在架构上要整体考虑，包括服务端和客户端，如何将应用进行分解，划分模块，考虑模块的独立性、可扩展行，以及可测试性。



##参考
* [Mastering Web Application Development with AngularJS](https://www.packtpub.com/web-development/mastering-web-application-development-angularjs)

* [http://stackoverflow.com/questions/14994391/how-do-i-think-in-angularjs-if-i-have-a-jquery-background](http://stackoverflow.com/questions/14994391/how-do-i-think-in-angularjs-if-i-have-a-jquery-background)