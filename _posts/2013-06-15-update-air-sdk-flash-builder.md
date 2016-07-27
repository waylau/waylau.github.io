---
layout: post
title: FlashBuilder4.7更新AIR SDK方法
date: 2013-06-15 01:54
author: admin
comments: true
categories: [AIR]
tags: [AIR, Flex, SDK]
---
默认情况下，Flash Builder 4.7安装了AIR SDK 3.4。如果你想使用别的SDK版本，你覆盖掉它就行了。下面就来介绍一下如何覆盖。
下载合适的AIR SDK文件：http://labs.adobe.com/downloads/asc2.html ，然后保存到AIR SDK的根目录（是SDK的根目录）。
注意了：这个AIR SDK包含了：Adobe AIR SDK、ActionScript编译器以及其它必需的组件和文件。
在进入以下步骤前，先退出Flash Builder：
在Flash Builder里更新AIR SDK的步骤：
1.（可选）备份旧的AIR SDK，把整个SDK目录都复制一份吧。AIR SDK的目录是在（因人而异，但大概一样）：
Windows 7: C:\Program Files (x86)\Adobe\Adobe Flash Builder 4.7\eclipse\plugins\com.adobe.flash.compiler_4.7.0.349722
Mac OS: /Applications/Adobe Flash Builder 4.7/eclipse/plugins/com.adobe.flash.compiler_4.7.0.349722
2. 在备份完后，删除SDK文件夹里的所有文件（译者注：这个SDK文件夹的名字为“AIRSDK”。只删除SDK文件夹里的文件，不要把SDK文件夹也删除了）。
3. 下载的SDK通常是一个压缩包（zip/tbz2）。解压这个包，提取所有文件，把文件复制到那个名为“AIRSDK”的文件夹。

参考Adobe 原文：http://helpx.adobe.com/flash-builder/kb/overlay-air-sdk-flash-builder.html
