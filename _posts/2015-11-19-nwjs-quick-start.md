---
layout: post
title: NW.js 入门指南
date: 2015-11-19 01:08
author: admin
comments: true
categories: [NW.js]
tags: [NW.js,Node.js]
---

本文介绍了 NW.js（node-webkit） 的基本知识，通过本入门指南的学习，可以让你快速构建一个  NW.js 的桌面应用。

<!-- more -->

## 简介 

[NW.js](http://nwjs.io/) （原名 node-webkit）是一个基于 `Chromium` 和 `node.js` 的应用运行时，通过它可以用  HTML 和 JavaScript 编写原生应用程序。它还允许您从 DOM 调用 [Node.js](https://nodejs.org) 的模块 ，实现了一个用所有 Web 技术来写原生应用程序的新的开发模式。

这里是[使用 NW.js 的应用和公司列表](https://github.com/nwjs/nw.js/wiki/List-of-apps-and-companies-using-nw.js)，可以看到 NW.js 实际应用效果。

## 功能特性

* 用现代 HTML5,CSS3,JS 和 WebGL 来编写应用程序。
* 完全支持 [Node.js APIs](https://nodejs.org/api/) 和所有其 [第三方模块](https://www.npmjs.com/).
* 良好的性能:Node 和 WebKit 运行在相同的线程:函数调用是更简洁;对象在同一堆可以互相引用;
* 容易打包和分发应用程序。
* 支持 Linux、Mac OS X 和 Windows

## 下载

地址: <http://dl.nwjs.io/>

截至目前（2015-11-19），各平台最新的下载版本为：

* **v0.12.3:** (Jul 31, 2015, based off of IO.js v1.2.0, Chromium 41.0.2272.76): [release notes](https://groups.google.com/d/msg/nwjs-general/hhXCS4aXGV0/TUQmcu5XDwAJ)  
 * Linux: [32bit](http://dl.nwjs.io/v0.12.3/nwjs-v0.12.3-linux-ia32.tar.gz) / [64bit](http://dl.nwjs.io/v0.12.3/nwjs-v0.12.3-linux-x64.tar.gz)
 * Windows: [32bit](http://dl.nwjs.io/v0.12.3/nwjs-v0.12.3-win-ia32.zip) / [64bit](http://dl.nwjs.io/v0.12.3/nwjs-v0.12.3-win-x64.zip)
 * Mac 10.7+: [32bit](http://dl.nwjs.io/v0.12.3/nwjs-v0.12.3-osx-ia32.zip) / [64bit](http://dl.nwjs.io/v0.12.3/nwjs-v0.12.3-osx-x64.zip)

* **v0.13.0-alpha5:** (Nov 2, 2015, based off of Node.js v5.0.0, Chromium 46.0.2490.80): [release notes](https://groups.google.com/d/msg/nwjs-general/YuwMHd_uvPM/pLFWG3vYBwAJ)  
 **NOTE** You might want the **SDK build**. Please read the release notes  
 * Linux: [32bit](http://dl.nwjs.io/v0.13.0-alpha5/nwjs-v0.13.0-alpha5-linux-ia32.tar.gz) / [64bit](http://dl.nwjs.io/v0.13.0-alpha5/nwjs-v0.13.0-alpha5-linux-x64.tar.gz)
 * Windows: [32bit](http://dl.nwjs.io/v0.13.0-alpha5/nwjs-v0.13.0-alpha5-win-ia32.zip) / [64bit](http://dl.nwjs.io/v0.13.0-alpha5/nwjs-v0.13.0-alpha5-win-x64.zip)
 * Mac 10.7+: [64bit](http://dl.nwjs.io/v0.13.0-alpha5/nwjs-v0.13.0-alpha5-osx-x64.zip)

* **0.8.6:** (Apr 18, 2014, based off of Node v0.10.22, Chrome 30.0.1599.66) **If your native Node module works only with Node v0.10, then you should use node-webkit v0.8.x, which is also a maintained branch. [More info](https://groups.google.com/d/msg/nwjs-general/2OJ1cEMPLlA/09BvpTagSA0J)**  
[release notes](https://groups.google.com/d/msg/nwjs-general/CLPkgfV-i7s/hwkkQuJ1kngJ)

 * Linux: [32bit](http://dl.node-webkit.org/v0.8.6/node-webkit-v0.8.6-linux-ia32.tar.gz) / [64bit](http://dl.node-webkit.org/v0.8.6/node-webkit-v0.8.6-linux-x64.tar.gz)
 * Windows: [win32](http://dl.node-webkit.org/v0.8.6/node-webkit-v0.8.6-win-ia32.zip)
 * Mac: [32bit, 10.7+](http://dl.node-webkit.org/v0.8.6/node-webkit-v0.8.6-osx-ia32.zip)

* **latest live build**: git tip version; build triggered from every git commit: http://dl.nwjs.io/live-build/

* [老版本](https://github.com/nwjs/nw.js/wiki/Downloads-of-old-versions)

## 快速入门

我们新建一个目录 `quick-start`，来代表项目名称。

### 客户端代码

在 `quick-start` 目录下，创建 `index.html`:

```html
<!DOCTYPE html>
<html>
  <head>
	<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>NW.js 快速入门|www.waylau.com</title>
  </head>
  <body>
    <h1>NW.js 快速入门</h1>
    We are using node.js <script>document.write(process.version)</script>.
	<br/>
	More demos,see <a href="http://www.waylau.com" title="更多示例">www.waylau.com</a>
  </body>
</html>
```

在 `quick-start` 目录下，创建 `package.json`:

```json
{
  "name": "nw-quick-start-demo",
  "version": "0.0.1",
  "main": "index.html"
}
```

### 运行

运行:  
```bash
$ /path/to/nw .  (假设当前目录包含 'package.json')
```

注意: 在 Windows 系统, 拖动包含 `package.json`文件夹 `quick-start` ，到 `nw.exe` 来打开它。

注意：在 OSX 系统,可执行编译文件是在隐藏目录的 .app 文件内。为了在 OSX  运行 node-webkit , 输入:  
`/path/to/nwjs.app/Contents/MacOS/nwjs .` *(假设当前目录包含 'package.json')*   

![](http://99btgc01.info/uploads/2015/11/nw.jpg)

### 效果

![](http://99btgc01.info/uploads/2015/11/nw1.jpg)

### 更多设置

将 `quick-start` 复制为另外要给项目 `quick-start-window`。

修改 `package.json` 来设置程序。

```josn
{
  "name": "nw-quick-start-window-demo",
  "version": "0.0.1",
  "main": "index.html",
  "window": {
    "title": "nw-quick-start-window-demo",
    "toolbar": false,
    "frame": true,
    "show_in_taskbar":true,
    "width": 800,
    "height": 500,
    "position": "mouse",
    "min_width": 400,
    "min_height": 200,
    "max_width": 800,
    "max_height": 600
  }
}
```

窗口外观常用属性包括：

* title : 字符串，设置默认 title。
* width/height : 主窗口的大小。
* toolbar : bool 值。是否显示导航栏。
* icon : 窗口的 icon。
* position :字符串。窗口打开时的位置，可以设置为“null”、“center”或者“mouse”。
* min_width/min_height : 窗口的最小值。
* max_width/max_height : 窗口显示的最大值。
* resizable : bool 值。是否允许调整窗口大小。
* always-on-top : bool 值。窗口置顶。
* fullscreen : bool 值。是否全屏显示。
* show_in_taskbar : 是否在任务栏显示图标。
* frame : bool 值。如果设置为 false，程序将无边框显示。


![](http://99btgc01.info/uploads/2015/11/nw2.jpg)

## 发布

本节以 Windows 环境下为例。

### 新建发布包

将下载的 NW.js 文件解压，复制一份作为项目的发布包模板，本例名称为`nwjs-v0.12.3-win-x64`

### 压缩

将 `quick-start` 内的文件压缩成 .zip　文件 `quick-start.zip`，修改文件后缀为 `quick-start.nw` (这个是管方的说法，其实不该后缀 直接是 quick-start.zip 可以是可以)

![](http://99btgc01.info/uploads/2015/11/001%281%29.jpg)


![](http://99btgc01.info/uploads/2015/11/002..jpg)

### 合成

将 `quick-start.nw` 放入发布包 `nwjs-v0.12.3-win-x64`，

![](http://99btgc01.info/uploads/2015/11/004.jpg)

在命令下，切换到  `nwjs-v0.12.3-win-x64` 目录，并执行

```
copy /b nw.exe+quick-start.nw quick-start.exe
```

![](http://99btgc01.info/uploads/2015/11/005.jpg)

此时，生成了可执行文件`quick-start.exe`，双击即可运行程序

![](http://99btgc01.info/uploads/2015/11/006.jpg)

### 删除冗余

最终的发布包，未减少体积，可以删除项目用不到的冗余文件，由于`nw.exe`、`quick-start.nw`文件已经合成为了 `quick-start.exe`,故删之。又因为该项目简单，没有用到媒体库，可以删除`nwjc.exe`、`ffmpegsumo.dll`等 

## 源码

见：<https://github.com/waylau/nwjs-demos>
中的 `quick-start` 和 `quick-start-window`。



