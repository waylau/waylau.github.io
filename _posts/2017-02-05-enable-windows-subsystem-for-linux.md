---
layout: post
title: 通过 WSL在Windows下愉快的玩耍Linux
date: 2017-02-05 00:22
author: admin
comments: true
categories: [WSL,Windows]
tags: [WSL,Windows]
---

WSL(Windows Subsystem for Linux) 是 在Windows系统中为那些熟悉Linux用户准备的诸多子系统功能。换言之，你可以在Windows环境下来执行Linux操作，运行 Linux程序。这对于Windows、Linux双系统有需求的用户来说是个不错的功能。本文详细介绍了如何使用 WSL。
   
<!-- more -->


## 条件限制


WSL 只在 Windows 10 版本以上提供该功能，所以，如果你想玩转 WSL ，请升级你的 Windows。

![](../images/post/20170205-win10-version.jpg)


## 切换到开发者模式下

![](../images/post/20170205-win10-dev.jpg)

## 启动 WSL 功能

在 选中 “Windows Subsystem for Linux”来启用  WSL 功能。

![](../images/post/20170205-win10-enable-wsl.jpg)

确认后，请重启计算机。


## 进入 Bash

进入 Bash，按照提示执行“y”，此时将会自动从 Windows 应用商店来下载 Linux （目前，暂时只支持Ubuntu系统）。


![](../images/post/20170205-win10-bash.jpg)


可能会由于网络原因安装会中断，请重复执行安装或者翻墙。

## 设置用户名密码

安装完成后，会提示你设置用户名和密码。当一切都完成后，你就可以像Linux 一样玩耍了。

![](../images/post/20170205-win10-bash-user.jpg)


## 参考资料

* https://msdn.microsoft.com/en-us/commandline/wsl/install_guide
 
 