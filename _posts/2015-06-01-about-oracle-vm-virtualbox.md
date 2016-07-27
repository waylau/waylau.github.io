---
layout: post
title: Oracle VM VirtualBox 安装和使用
date: 2015-06-01 01:41
author: admin
comments: true
categories: [VirtualBox]
tags: [VirtualBox]
---
 
VirtualBox 是一款开源虚拟机软件。VirtualBox 是由德国 Innotek 公司开发，由Sun Microsystems 公司出品的软件，使用 Qt 编写，在 Sun 被 Oracle 收购后正式更名成 Oracle VM VirtualBox。Innotek 以 GNU General Public License (GPL) 释出 VirtualBox，并提供二进制版本及 OSE 版本的代码。使用者可以在VirtualBox上安装并且执行 Solaris、Windows、DOS、Linux、OS/2 Warp、BSD 等系统作为客户端操作系统。

VirtualBox 号称是最强的免费虚拟机软件，它不仅具有丰富的特色，而且性能也很优异！它简单易用，可虚拟的系统包括 Windows（从 Windows 3.1 到 Windows8、Windows Server 2012，所有的 Windows 系统都支持）、Mac OS X（32bit和64bit都支持）、Linux（2.4和2.6）、OpenBSD、Solaris、IBM OS2 甚至 Android 4.0 系统等操作系统！使用者可以在 VirtualBox 上安装并且运行上述的这些操作系统！ 与同性质的 VMware 及 Virtual PC 比较下，VirtualBox 独到之处包括远端桌面协定（RDP）、iSCSI 及 USB 的支持，VirtualBox 在客户端操作系统上已可以支持USB 2.0的硬件装置，不过要安装 VirtualBox Extension Pack。

<!-- more -->

##	环境

* CentOS 7.1 (64-bit system)
* Oracle VM VirtualBox 4.3.28 ( Windows)

## 下载

下载 Oracle VM VirtualBox 

地址 <http://www.oracle.com/technetwork/server-storage/virtualbox/downloads/index.html>

## 安装

双击安装包进行安装，一路下一步即可

## 解决 Virtualbox 安装系统时出错

运行 Virtualbox 去安装系统时出错：

    Failed to open a session for the virtual machine，Unable to load R3 module xxxx/VBoxDD.DLL(VBoxDD)，GetLastError=126，(VERR_MODULE_NOT_FOUND)
    
使用 UniversalThemePatcher 这个工具还原系统主题的
<http://pan.baidu.com/s/1tvrXk>

## 开启Virtualization Technology虚拟化技术功能

用来支持 虚拟机安装64位操作系统。

比如 Virtualization Technology 关闭时，我只能选 32 位的系统

![](http://99btgc01.info/uploads/2015/06/006.png)

在BIOS 设置中，在CPU Setup 中选择开启 Intel Virtual Technology，

![](http://99btgc01.info/uploads/2015/06/007.png)

这样，就可以选择64位的系统了

## 安装操作系统

启用网桥，这样，虚拟机和物理机之间就能互相通信了。如果有特殊需要（比如公司设置了防火墙），可能还要需要配置一个固定的 Mac 地址

![](http://99btgc01.info/uploads/2016/04/001%281%29.jpg)
![](http://99btgc01.info/uploads/2016/04/002%281%29.jpg)

启动

![](<http://99btgc01.info/uploads/2015/06/009.png>)

选择本地操作系统 iso

![](<http://99btgc01.info/uploads/2015/06/010.png>)

![](<http://99btgc01.info/uploads/2015/06/011.png>)

![](<http://99btgc01.info/uploads/2015/06/012.png>)


## CentOS 配置

有关 CentOS 的配置可以参考 <http://www.waylau.com/centos-7-installation-and-configuration/>

## 相关错误解决

### Unable to load R3 module

错误提示：

    Unable to load R3 module C:\Program Files\Oracle\VirtualBox/
    
    VBoxDD.DLL (VBoxDD): GetLastError=1790 (VERR_UNRESOLVED_ERROR).
    返回 代码:  E_FAIL   (0x80004005)
    组件:  Console
    界面:  IConsole   {8ab7c520-2442-4b66-8d74-4ff1e195d2b6}
    
解决方法：

1. 其它的电脑上面将上面的`C:\Windows\system32\uxtheme.dll` 文件拷贝过来 覆盖本机的 `uxtheme.dll`
2. 使用 UniversalThemePatcher 还原破解的主题，全部恢复为“未破解”状态即可。

![](http://i12.tietuku.com/888c0ca2c12436db.png)


上述两个资源都 可以在 <http://pan.baidu.com/s/1hra3PjQ> 进行下载。
