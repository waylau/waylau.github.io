---
layout: post
title: 仓颉编程语言的仓颉工具链和DevEco Studio Cangjie Plugin是什么关系？为什么要装这么多插件？
date: 2025-06-30 00:22
author: admin
comments: true
categories: [Cangjie]
tags: [Cangjie,HarmonyOS]
---

在“[HarmonyOS NEXT+AI大模型打造智能助手APP(仓颉版)](https://coding.imooc.com/class/927.html)”课程里面，有学员提到了这样一个问题：
安装了仓颉工具链，为什么还要安装DevEco Studio Cangjie Plugin？DevEco Studio会去调用仓颉工具链的SDK吗？好像没有看到那里有和这个相关的设置。



<!-- more -->




![](https://img1.sycdn.imooc.com/9caeca6709eb3bf806220180.jpg)

这里就这位同学的问题，统一做下回复，以方便其他同学参考。往期问答，可以在我主页查到。




## 是哪两套环境？

在课程的“第3章 智能AI助手项目--开发环境搭建”一章里面我们安排了以下内容：

* 3.1 本章导学
* 3.2 注册华为账号进行实名认证
* 3.3 搭建仓颉编程语言开发环境
* 3.4 CJPM基本使用方法
* 3.5 【实战】开发仓颉“Hello World”应用
* 3.6 分析仓颉“Hello World”应用结构
* 3.7 下载安装开发工具DevEco Studio
* 3.8 设置DevEco Studio
* 3.9 下载安装DevEco Studio Cangjie Plugin
* 3.10 【实战】创建一个HarmonyOS应用“Hello World”
* 3.11 HarmonyOS应用签名
* 3.12 在本地真机运行HarmonyOS应用
* 3.13 使用本地模拟器运行HarmonyOS应用
* 3.14 掌握DevEco Studio编辑器常用功能
* 3.15 HarmonyOS应用结构详解
* 3.16 掌握HarmonyOS的核心组件Ability
* 3.17 理解应用级及组件级配置
* 3.18 理解HarmonyOS打包原理
* 3.19 如何提升UI开发效率和体验？
* 3.20 本章小结
* 3.21 习题：创建一个HarmonyOS应用

可以看到这里其实是有介绍两套环境的设置：一个是纯粹的仓颉环境，一个是仓颉鸿蒙环境。两套环境独立的，没有关联关系。


## 纯粹的仓颉环境

纯粹的仓颉环境主要的安装步骤为：

* 3.3 搭建仓颉编程语言开发环境


主要涉及安装仓颉工具链及VSCode仓颉插件。仓颉工具链简单理解就是仓颉的SDK，类似于Java的JDK，用于编译、运行纯粹的仓颉程序。


其中，VSCode仓颉插件是指支持可以在VSCode开发纯粹的仓颉程序。


## 仓颉鸿蒙环境

仓颉鸿蒙环境主要的安装步骤为：

* 3.7 下载安装开发工具DevEco Studio
* 3.8 设置DevEco Studio
* 3.9 下载安装DevEco Studio Cangjie Plugin


主要涉及安装DevEco Studio及DevEco Studio Cangjie Plugin插件。上述工具主要是为了支持在DevEco Studio里面开发仓颉鸿蒙应用。类似于Android Studio。

## 为什么是两套环境？


那是两套环境，一个是纯粹的仓颉环境，一个是仓颉鸿蒙环境。两套环境独立的，没有关联关系。

如果只是想学习仓颉编程语言的语法，那么只需要纯粹的仓颉环境。课程“第4章 智能AI助手--快速带你上手仓颉编程语言”会有完整的学习路径安排，从0到1，可以系统掌握仓颉编程语言。可以通过本章的学习，掌握动物发声模拟器的开发，理解仓颉集合类、异常处理、包的管理，掌握大型程序代码组织管理、熟悉I/O操作、读写文件操作，掌握网络编程，甚至是可以通过仓颉编程语言创建TCP服务器、UDP服务器，构建HTTP服务等。


如果是想用仓颉编程语言的开发鸿蒙应用，则需要安装颉鸿蒙环境。大家都知道DevEco Studio是开发鸿蒙应用的必备的IDE。由于仓颉编程语言暂未正式发布，因此是需要通过插件方式（DevEco Studio Cangjie Plugin）来支持在DevEco Studio开发基于仓颉编程语言的鸿蒙应用。课程第5-11章也安排了相应的实战训练。



## 未来会怎么样？

目前，多套环境多个插件确实是给很好鸿蒙应用开发者带来了很多困扰，但这只是暂时的。正如我在“[华为自研仓颉编程语言将开源，未来与ArkTS同等地位](https://www.imooc.com/article/382571)”一文中提到的那样，未来随着仓颉编程语言的不断完善，势必会与ArkTS处于同等地位，那么DevEco Studio就会天然支持基于仓颉编程语言的鸿蒙应用，无需再安装那么多插件了。

仓颉编程语言未来可期，大家准备好了吗？

## 鸿蒙编程语言的选择


在了解上述鸿蒙编程语言的区别之后，相信各位对于鸿蒙编程语言的选择有了自己的判断能力。读者在选择语言的时候，可以结合自身的情况，综合考虑以下几个方面。

### 1. 个人的偏好

开发者对于编程语言是有一定的偏好的，选ArkTS或是Java也是可以尊重个人的喜好。

如果你熟悉Java或者Android则可以选Java；如果你熟悉JS或者TS，就可以选ArkTS；如何你熟悉Rust，那么仓颉也是不错的选择。

### 2. 职业的需要

如果想要实现一个强大的完整的鸿蒙应用功能，且能最大限度适配现有市面上的Android设备，那么现阶段Java是个不错的选择。不过需要注意的是，鸿蒙从API 8之后，就不再支持Java了。有关Java开发鸿蒙应用的相关内容，可以参阅笔者所著的《鸿蒙HarmonyOS手机应用开发实战》《鸿蒙HarmonyOS应用开发从入门到精通战》以及视频教程“[鸿蒙系统实战短视频App 从0到1掌握HarmonyOS](https://coding.imooc.com/class/674.html)”。

ArkTS目前是处于鸿蒙生态主推的地位，新商业项目的创建建议基于ArkTS来构建。有关ArkTS开发鸿蒙应用的相关内容，可以参阅笔者所著的《鸿蒙HarmonyOS应用开发入门》《鸿蒙HarmonyOS应用开发从入门到精通（第2版）》《鸿蒙之光HarmonyOS NEXT原生应用开发入门》以及视频教程“[鸿蒙零基础快速实战-仿抖音App开发（ ArkTS版 ）](https://coding.imooc.com/class/843.html)”。




如果你是纯粹想学习一门新语言，想不断跟随语言的演进，那么仓颉也是不错的选择。从未来发展上看，仓颉有可能会替补ArkTS，作为主力开发语言。不过需要注意的是，仓颉目前还只是开发者预览版本，API处于一种不稳定的状态，功能也不太完善，不建议在商业场景使用。有关仓颉编程语言的相关内容，可以参阅笔者所著的《仓颉编程从入门到实践》以及视频教程“[HarmonyOS NEXT+AI大模型打造智能助手APP(仓颉版)](https://coding.imooc.com/class/927.html)”。






## 参考资料

更多开源教程，详见：

* 《跟老卫学HarmonyOS开发》：<https://github.com/waylau/harmonyos-tutorial>
* 《跟老卫学仓颉编程语言开发》：<https://github.com/waylau/cangjie-programming-language-tutorial>