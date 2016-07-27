---
layout: post
title: Activiti 5.17.0 发布
date: 2014-12-19 01:21
author: admin
comments: true
categories: [Activiti]
tags: [Activiti]
---

##Activiti 5.17.0

Activiti 5.17.0 发布 (2014-12-18)，此版本值得关注的亮点：

* 我们介绍了一个完全测试和全新的 Async executor （异步执行器）,它取代旧的Job executor。新的异步执行程序使用更少的数据库查询来执行异步工作并拥有更好的性能。默认 Activiti 引擎仍然使用旧的工作执行者,所以你必须显式地选择的新的异步执行器通过在流程引擎配置设置 asyncExecutorEnabled 属性。更多细节可以查看的[用户指南](https://github.com/waylau/activiti-5.x-user-guide)高级功能章节。
* Activiti Modeler 完全修正,使用 Angular JS 实现。这是捐赠的Alfresco Activiti BPMN 编辑器,该编辑器的[Alfresco Activiti Enterprise](http://www.alfresco.com/products/activiti)版本的一部分提供。新的 Angular JS Activiti Modeler 是 LGPL 许可。注意,一些对话框还没有移植到新的 Activiti Modeler 中,但你还会发现很多新的功能,包括 Mule 和 Camel 任务，能够给专用网关定义一个顺序流命令。真的很感激任何帮助和添加的新特性。
* [用户指南](https://github.com/waylau/activiti-5.x-user-guide)修订了 AsciiDoc 的重写

<!-- more -->

* 现在可以启动一个流程实例 跳过 基于 skipExpression 的任务。这个非常方便,当你迁移流程实例到 Activiti 引擎(感谢 Robert Hafner 的 pull 请求)。
* 我们添加了一个名为 activiti-jmx 的 Activiti 项目的新模块,启动Activiti 引擎的 JMX 。读 [Saeids博客](http://tech.blog.saeidmirzaei.com/?p=251)获取更多细节(感谢Saeid)。
* 对于流程实例和普通的执行，变量的获取已经被优化
* 数据库升级脚本使用一个不同的比 JAR 小的版本。发布版是  5.17.0, 5.17.0.2是数据库版本。这个版本让我们发布快照，并且能够从一个快照版本升级到一个稳定的版本。5.17.1释放数据库版本将是 5.17.1.x,x可以是任何数字。所以只有最后一个小版本号可以跟 Activiti 引擎发布版本不同

更多内容请看[发布说明](http://jira.codehaus.org/secure/ReleaseNote.jspa?projectId=12091&version=20537)。

##如何快速开始 Activiti

参见[Activiti 5 下载、安装和运行](http://www.waylau.com/activiti-5-download-install-run/)

##参考：

* [https://github.com/Activiti/Activiti/releases](https://github.com/Activiti/Activiti/releases)
* 《[Activiti 5.x 用户指南](https://github.com/waylau/activiti-5.x-user-guide)》 中文翻译