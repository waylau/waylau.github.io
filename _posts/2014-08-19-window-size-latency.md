---
layout: post
title:  $(window).resize() 执行延迟
date: 2014-08-19 01:40
author: admin
comments: true
categories: [HTML]
tags: [HTML,window,resize,延迟]
---
#问题
` $(window).resize()`  是监听窗口大小缩放，而后执行动作。最近在用`echarts`报表时，发现表报的缩放跟不上窗口的缩放节奏，比窗口的缩放总是慢一步。代码

    $(window).resize(function(){

        myChart.resize();

    });

#解决
加个时间，延迟执行就好了

	var timer = 0;

    $(window).resize(function(){

        clearTimeout(timer);

        timer = setTimeout(function() {
            myChart.resize();

        }, 200);

    });

 