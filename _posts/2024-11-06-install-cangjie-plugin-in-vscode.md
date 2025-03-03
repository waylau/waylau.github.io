---
layout: post
title: 仓颉编程语言开发环境搭建（安装VSCode仓颉插件）
date: 2024-11-06 00:22
author: admin
comments: true
categories: [Cangjie]
tags: [Cangjie,HarmonyOS,NEXT]
---


在前文《[HarmonyOS NEXT仓颉编程语言开发环境搭建（安装DevEco Studio Cangjie Plugin）](https://waylau.com/install-deveco-studio-cangjie-plugin/)》《[仓颉编程语言开发环境搭建（安装仓颉工具链）](https://waylau.com/install-cangjie-lang)》，已经介绍了如何使用DevEco Studio搭建仓颉编程语言开发环境以及如何安装仓颉工具链。在 VSCode 中安装仓颉插件，以及如何使用插件提供的功能。



<!-- more -->


## VSCode仓颉插件概述

仓颉语言提供了 Visual Studio Code（简称 VSCode） 插件，通过在 VSCode 中安装仓颉插件和仓颉 SDK，可以为开发者提供语言服务、工程管理、编译构建、调试服务、格式化、静态检查、代码覆盖率统计的功能。本文档介绍如何在 VSCode 中安装仓颉插件，以及如何使用插件提供的功能。



## 下载安装包

下载地址为<https://cangjie-lang.cn/download>，下载完成之后，会得到一个Cangjie-vscode-x.y.z.tar.gz安装包。


## 安装

选择 tar.gz 格式的安装包，请将它解压到适当目录。在安装包中，会提供一个.vsix 文件。

在 VSCode EXTENTIONS操作栏中选择安装本地插件，找到要安装的插件.vsix，点击确定即可安装。

## 配置

根据《[仓颉编程语言开发环境搭建（安装仓颉工具链）](https://waylau.com/install-cangjie-lang)》，确认在已经安装了仓颉工具链之后，就可以进行VSCode仓颉插件配置。

以Windows为例，右键点击VSCode仓颉插件，选择 Extension Settings，进入配置页面。


* `Cangjie Sdk Path: CJNative Backend`选项，输入 CJNative 后端 SDK 文件（即安装仓颉工具链安装目录）所在绝对路径，比如本例“D:\dev\cangjie\Cangjie-0.53.13-windows_x64\cangjie”。
* `Cangjie Sdk: Option`选项，选择后端类型为 CJNative（默认是此选项）


## 验证

用VSCode打开一个仓颉应用，比如《[跟老卫学仓颉编程语言开发]（<https://github.com/waylau/cangjie-programming-language-tutorial>）》所提供的“hello_world”应用源码，点击“Run > Run Without Debugging”进行运行。

运行成功，可以看到控制台打印如下信息：

```
PS D:\workspace\gitee\cangjie-programming-language-tutorial-book\samples\hello_world>
PS D:\workspace\gitee\cangjie-programming-language-tutorial-book\samples\hello_world> Hello World!
```

## 参考资料


* 本文同步至：<https://waylau.com/install-cangjie-plugin-in-vscode/>
* 《跟老卫学HarmonyOS开发》 开源免费教程，<https://github.com/waylau/harmonyos-tutorial>
* 《鸿蒙HarmonyOS手机应用开发实战》（清华大学出版社）
* 《鸿蒙HarmonyOS应用开发从入门到精通战》（北京大学出版社）
* “鸿蒙系统实战短视频App 从0到1掌握HarmonyOS”（<https://coding.imooc.com/class/674.html>）
* 《鸿蒙HarmonyOS应用开发入门》（清华大学出版社）
* “鸿蒙零基础快速实战-仿抖音App开发（ArkTS版）”（<https://coding.imooc.com/class/843.html>）
* 《跟老卫学仓颉编程语言开发》（<https://github.com/waylau/cangjie-programming-language-tutorial>）