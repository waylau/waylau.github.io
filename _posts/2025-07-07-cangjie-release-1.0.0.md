---
layout: post
title: 仓颉编程语言（Cangjie）正式发布1.0.0 LTS版本
date: 2025-07-07 00:22
author: admin
comments: true
categories: [Cangjie]
tags: [Cangjie]
---

仓颉编程语言的首个长期支持（Long-Term Support, LTS）版本已于2025年7月1日正式发布。仓颉最早是在2024年6月的华为开发者大会亮相，定位是下一代编程语言。笔者估计，本次LTS版本发布，是为了配合将于本月底[仓颉编程语言开源](https://developer.huawei.com/consumer/cn/forum/topic/0210186139141263055?fid=0109140870620153026)事宜。

本文主要介绍仓颉编程语言的特性及安装。

<!-- more -->

## 仓颉编程语言简介


仓颉编程语言是华为自研的一种面向全场景应用开发的通用编程语言，可以兼顾开发效率和运行性能，并提供良好的编程体验，主要具有如下特点：

* 多后端支持：仓颉编程语言支持 CJNative 和 CJVM 两种后端。其中 CJNative 后端将代码编译为原生二进制代码，直接在操作系统层面上运行；CJVM 后端将代码编译为字节码，基于 VM（虚拟机）进行运行。本次发布仅提供 CJNative 后端 SDK，CJVM 后端 SDK 敬请期待。
* 语法简明高效：仓颉编程语言提供了一系列简明高效的语法，旨在减少冗余书写、提升开发效率，例如插值字符串、主构造函数、Flow 表达式、match 和重导出等语法，让开发者可以用较少编码表达相关逻辑。
* 多范式编程：仓颉编程语言支持函数式、命令式和面向对象等多范式编程，融合了高阶函数、代数数据类型、模式匹配、泛型等函数式语言的先进特性，还有封装、接口、继承、子类型多态等支持模块化开发的面向对象语言特性，以及值类型、全局函数等简洁高效的命令式语言特性。开发者可以根据开发偏好或应用场景，选用不同的编程范式。
* 类型安全：仓颉编程语言是静态强类型语言，通过编译时类型检查尽早识别程序错误，降低运行时风险，也便于代码维护。同时，仓颉编译器提供了强大的类型推断能力，可以减少类型标注工作，提高开发效率。
* 内存安全：仓颉编程语言支持自动内存管理，并在运行时进行数组下标越界检查、溢出检查等操作，确保运行时内存安全。
* 高效并发：仓颉编程语言提供了用户态轻量化线程（原生协程），以及简单易用的并发编程机制，保证并发场景的高效开发和运行。
* 兼容语言生态：仓颉编程语言支持和 C 等编程语言的互操作，并采用便捷的声明式编程范式，可实现对其他语言库的高效复用和生态兼容。
* 领域易扩展：仓颉编程语言提供了基于词法宏的元编程能力，支持在编译时变换代码。此外，还提供了尾随 lambda、属性、操作符重载、部分关键字可省略等特性，开发者可由此深度定制程序的语法和语义，这有利于内嵌式领域专用语言（Embedded Domain Specific Languages，EDSL）的构建。
* 助力 UI 开发：UI 开发是构建端侧应用的重要环节，基于仓颉编程语言的元编程和尾随 lambda 等特性，用户可以搭建声明式 UI 开发框架，提升 UI 开发效率和体验。
* 内置库功能丰富：仓颉编程语言提供了功能丰富的内置库，涉及数据结构、常用算法、数学计算、正则匹配、系统交互、文件操作、网络通信、数据库访问、日志打印、解压缩、编解码、加解密和序列化等功能。

## 版本


仓颉编程语言提供三个版本（LTS、STS 和 Nightly Build），每个通道均提供可以在Linux、Windows以及Mac上安装使用的软件包与帮助开发者在VScode平台上搭建开发环境的插件。每个版本通道更新/维护计划有所差异，具体计划根据各版本通道描述为准。


从官网了解到，截止目前仓颉编程语言也就发布过三个版本号，各个版本之间估计变更内容不是很多。

![三个版本号](/images/post/20250707-cangjie-release-002.jpg)


## 安装包

安装包在命名上更加规范了。安装包可以在<https://cangjie-lang.cn/download>，找到分为了仓颉SDK（仓颉工具链）和仓颉插件。


![分为了仓颉SDK（仓颉工具链）和仓颉插件](/images/post/20250707-cangjie-release-001.jpg)


* 仓颉SDK（仓颉工具链）：cangjie-sdk-windows-x64-1.0.0.zip
* 仓颉语言 VS Code 插件：cangjie-vscode-1.0.0.tar.gz，该插件可以通过离线方式安装。插件也可以无需下载，直接可以在VS Code插件市场上找到直接安装。

## Windows 平台上的安装、卸载

### 安装指导

#### 1. 解压安装包


如果选择 `zip` 格式的安装包（例如 `cangjie-sdk-windows-x64-x.y.z.zip`），请将它解压到适当目录（比如本例`D:\dev\cangjie\cangjie-sdk-windows-x64-1.0.0\cangjie`），在安装包中，仓颉为开发者提供了三种不同格式的安装脚本，分别是 `envsetup.bat`，`envsetup.ps1` 和 `envsetup.sh`，可以根据使用习惯及环境配置，选择一种执行：

    - 若使用 Windows 命令提示符（CMD）环境，请执行：

        ```bash
        path\to\cangjie\envsetup.bat
        ```

    - 若使用 PowerShell 环境，请执行：

        ```bash
        . path\to\cangjie\envsetup.ps1
        ```

    - 若使用 MSYS shell、bash 等环境，请执行：

        ```bash
        source path/to/cangjie/envsetup.sh
        ```

    为了验证是否安装成功，请在以上命令环境中继续执行 `cjc -v` 命令，如果输出了仓颉编译器版本信息，表示已经成功安装了仓颉工具链。

**值得注意的是**，基于 zip 安装包和执行脚本的安装方式，类似于 Linux 平台，即 envsetup 脚本所配置的环境变量，只在当前命令行环境中有效，如果打开新的命令行窗口，需要重新执行 envsetup 脚本配置环境。此时，若想使仓颉工具链的环境变量配置在命令提示符或终端启动时自动生效，可以设置环境变量

#### 2. 配置 CANGJIE_HOME 变量

“系统变量”（为系统所有用户进行配置）区域中，点击 “新建” 按钮，并在 “变量名” 字段中输入 `CANGJIE_HOME`。在 “变量值” 字段中输入仓颉安装包的解压路径，（比如本例`D:\dev\cangjie\cangjie-sdk-windows-x64-1.0.0\cangjie`）。配置完成后，请在 “编辑用户变量” 或 “编辑系统变量” 窗口界面确认路径正确，然后点击 “确定” 按钮。


![配置 CANGJIE_HOME 变量](/images/post/20250707-cangjie-release-003.jpg)



在“系统变量”（为系统所有用户进行配置）区域中，找到并选择 Path 变量，点击 “编辑” 按钮，进入 “编辑环境变量” 窗口。点击 “编辑文本” 按钮，附加以下内容`;%CANGJIE_HOME%\bin;%CANGJIE_HOME%\tools\bin;%CANGJIE_HOME%\tools\lib;%CANGJIE_HOME%\runtime\lib\windows_x86_64_llvm`。设置完成后可能需要重启命令行窗口或重启系统以让设置生效。


![添加到Path](/images/post/20250707-cangjie-release-004.jpg)


配置完成后 Windows 命令提示符（CMD）或 PowerShell 启动即可直接使用仓颉编译工具链。


```bash
C:\Users\wayla>cjc -v
Cangjie Compiler: 1.0.0 (cjnative)
Target: x86_64-w64-mingw32
```


### 卸载与更新


如果选择 `zip` 格式的安装包进行的安装，删除仓颉工具链的安装包目录，同时移除上述环境变量设置（若有），即可完成卸载。

若需要更新仓颉工具链，需要先卸载当前版本，然后按上述指导重新安装最新版本的仓颉工具链。

### 配置仓颉语言 VS Code 插件


安装过程，参考往期[博客](https://waylau.com/install-cangjie-plugin-in-vscode/)。

主要配置如下：

![配置仓颉语言 VS Code 插件](/images/post/20250707-cangjie-release-005.jpg)


能呼出如下界面，则证明安装成功：

![验证仓颉语言 VS Code 插件](/images/post/20250707-cangjie-release-006.jpg)




### 视频演示

详见B站：<https://www.bilibili.com/video/BV1mz36zMEdz/>

## 参考引用



* [华为自研仓颉编程语言将开源，未来与ArkTS同等地位](https://developer.huawei.com/consumer/cn/forum/topic/0210186139141263055?fid=0109140870620153026)
* [仓颉编程语言开发环境搭建（安装VSCode仓颉插件）](https://waylau.com/install-cangjie-plugin-in-vscode/)
* 《跟老卫学仓颉编程语言开发》开源免费教程， <https://github.com/waylau/cangjie-programming-language-tutorial>
* “HarmonyOS NEXT+AI大模型打造智能助手APP(仓颉版)”<https://coding.imooc.com/class/927.html>