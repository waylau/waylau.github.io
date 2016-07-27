---
layout: post
title: Flex4 发布后访问，初始化极其缓慢的问题 解决方案
date: 2011-07-14 01:41
author: admin
comments: true
categories: [Flex]
tags: [Flex,优化]
---
* Flex项目初始化太慢！Flex4为了加快加载速度使用了RSL，即把可以共享的框架、类库单独生成文件，并在请求时缓存到 Flash cache中，这样只要是同域的Flex项目就都可以使用而不必重新加载从而加快了加载时间。而问题也恰恰处在这个默认设置上，这些共享文件的优先位置是 从Adobe的服务端上下载，正是这个请求下载过程造成了加载奇慢。解决的办法就是将这些文件的位置优先指向自己的服务器（本地），Flash builder的Release 发布也可以生成这些文件，只要将这些.swz文件上传至服务器即可。

* 如何设置RSL路径。网上找了半天，大多数是flex-config.xml，但这个是Flex3的，Flex4 默认是不使用这个文件了。在Adobe的帮助文档上找到了Customize the deployment location of the framework RSLs（http://help.adobe.com/en_US/flex/using/WS2db454920 e96a9e51e63e3d11c0bf69084-7add.html）设置方法如图，打开项目--属性--flex构建路径--库路径标签，点开 Flex4.1 选择framework.swc的链接类型--编辑--弹出库路径选项框。
可以看到默认的adobe的server路径在第一个，第二个是本地路径（要求与swf在同目录），将第二个调到第一位置（图中已调整）。当然也可以自定义路径，可以是相对的或绝对地址。点击确定保存设置。
这样依次设置
framework_4.6.0.23201.swz
textLayout_2.0.0.232.swz
等等

* 最后点击项目--导出发行版，在bin-releas下可以看到生成的.swz文件，第一次要把所有的文件上传到网站，以后只要是同域的其他Flex项目指定好RSL路径，就不必再上传.swz文件了，这样大大减小了swf文件的大小，加快了加载速度。

到此解决了开始的问题，同时使得RSL的优点也得到体现。

查考：
<http://forums.adobe.com/message/2428795#2428795>
<http://blog.163.com/vituk93@126/blog/static/170958034201282222046364/>
