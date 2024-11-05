---
layout: post
title: 仓颉编程语言开发环境搭建（安装仓颉工具链）
date: 2024-09-04 00:22
author: admin
comments: true
categories: [Cangjie]
tags: [Cangjie,HarmonyOS,NEXT]
---


在前文《[HarmonyOS NEXT仓颉编程语言开发环境搭建（安装DevEco Studio Cangjie Plugin）](https://waylau.com/install-deveco-studio-cangjie-plugin/)》，已经介绍了如何使用DevEco Studio搭建仓颉编程语言开发环境。本节介绍如何使用通用方式安装仓颉工具链。



<!-- more -->


## 仓颉工具链概述

在开发仓颉程序时，必用的工具之一是仓颉编译器，它可以将仓颉源代码编译为可运行的二进制文件，但现代编程语言的配套工具并不止于此，实际上，仓颉为开发者提供了编译器、调试器、包管理器、静态检查工具、格式化工具和覆盖率统计工具等一整套仓颉开发工具链，同时提供了友好的安装和使用方式，基本能做到“开箱即用”。

目前仓颉工具链已适配部分版本的 Linux 和 Windows 平台，但是仅针对部分 Linux 发行版做了完整功能测试，详情可参阅附录Linux 版本工具链的支持与安装章节，在暂未进行过完整功能测试的平台上，仓颉工具链的功能完整性不受到保证。此外，当前 Windows 平台上的仓颉编译器基于 MinGW 实现，相较于 Linux 版本的仓颉编译器，功能会有部分欠缺。



## 下载安装包

仓颉编程语言提供三个版本通道（LTS、Beta 和 Dev），每个通道均提供可以在Linux、Windows以及Mac上安装使用的软件包与帮助开发者在VScode平台上搭建开发环境的插件。下载地址为<https://cangjie-lang.cn/download>


以Windows环境为例，下载完成之后，会得到一个Cangjie-x.y.z-windows_x64.zip安装包。


## 安装

选择 zip 格式的安装包，请将它解压到适当目录，在安装包中，仓颉为开发者提供了三种不同格式的安装脚本，分别是 envsetup.bat，envsetup.ps1 和 envsetup.sh，可以根据使用习惯及环境配置，选择一种执行：

若使用 Windows 命令提示符（CMD）环境，请执行：

```
path\to\cangjie\envsetup.bat
```

若使用 PowerShell 环境，请执行：

```
. path\to\cangjie\envsetup.ps1
```

若使用 MSYS shell、bash 等环境，请执行：

```
source path/to/cangjie/envsetup.sh
```


**注意**：基于 zip 安装包和执行脚本的安装方式，类似于 Linux 平台，即 envsetup 脚本所配置的环境变量，只在当前命令行环境中有效，如果打开新的命令行窗口，需要重新执行 envsetup 脚本配置环境。


envsetup.bat内容如下：

```
@REM Copyright Huawei Technologies Co., Ltd. 2022-2022. All rights reserved.
@REM This script needs to be placed in the root directory of installation of Cangjie compiler and libraries.

@echo off
REM Set CANGJIE_HOME to the path of this batch script.
set "CANGJIE_HOME=%~dp0"

REM Windows searches for both binaries and libs in %Path%
set "PATH=%CANGJIE_HOME%runtime\lib\windows_x86_64_llvm;%CANGJIE_HOME%bin;%CANGJIE_HOME%tools\bin;%CANGJIE_HOME%tools\lib;%PATH%;%USERPROFILE%\.cjpm\bin"
```




## 验证

仓颉SDK目录下，会有一个仓颉编译器，执行“cjc -v”来验证安装是否完成：

```
>cjc -v

Cangjie Compiler: 0.53.13 (cjnative)
Target: x86_64-w64-mingw32
```

## 参考资料


* 本文同步至：<https://waylau.com/install-cangjie-lang/>
* 《跟老卫学HarmonyOS开发》 开源免费教程，<https://github.com/waylau/harmonyos-tutorial>
* 《鸿蒙HarmonyOS手机应用开发实战》（清华大学出版社）
* 《鸿蒙HarmonyOS应用开发从入门到精通战》（北京大学出版社）
* “鸿蒙系统实战短视频App 从0到1掌握HarmonyOS”（<https://coding.imooc.com/class/674.html>）
* 《鸿蒙HarmonyOS应用开发入门》（清华大学出版社）
* “2024鸿蒙零基础快速实战-仿抖音App开发（ArkTS版）”（<https://coding.imooc.com/class/843.html>）
* 《跟老卫学仓颉编程语言开发》（<https://github.com/waylau/cangjie-programming-language-tutorial>）