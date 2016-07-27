---
layout: post
title:   SecurityError Error 2148 SWF 不能访问本地资源
date:   2013-04-13 01:29
author: admin
comments: true
categories: [Flash]
tags: [Flash]
---
##错误提示：

SWF 文件不能被本地访问 不能访问本地 只有仅限于文件系统的 SWF 文件和可信的本地 SWF 文件可以访问本地资源

##错误信息：

SecurityError: Error #2148: SWF 文件 D:\demo\demo.swf 不能访问本地资源 D:\demo\demo\bin-debug\textLayout_4.0.0.10485.swf。只有仅限于文件系统的 SWF 文件和可信的本地 SWF 文件可以访问本地资源。

##解决方法：

###第一种办法

在Flash插件上右键->全局设置->高级->开发人员工具->受信任的位置设置->添加 ，然后添加你的文件或者文件夹即可。

说明：在本地测试期间，Flash Player 可以显示阻止应用程序正常运行的安全警告。您可以使用“受信任位置设置”面板将您自己的 SWF 或 FLV 内容指定为可信内容，从而解决这一问题。您可以将个别文件的路径指定为可信路径，或者指定文件夹为可信文件夹。可信文件夹中的所有文件及其任何子文件夹也会受信任。使用此选项卡可指定您计算机上包含您信任的 SWF 或 FLV 内容的位置。可信内容不遵循阻止内容访问 Internet 和本地计算机文件系统的安全规则。

###第二种方法:

在C:\WINDOWS\system32\Macromed\Flash\FlashPlayerTrust目录下添加一个文件，比如：1.txt（文件名可任意起），文件内容为项目的路径，当然也可以设置为“D:\” 说明： 为了让你的Flash在用户硬盘中拥有同时访问网络和本地的能力，你必须在用户计算机的FlashPlayerTrust目录中为你的Flash添加权限. FlashPlayerTrust 配置文件。 这些是列出受信任路径的简单文本文件。这些文件由可执行的安装程序创建。当安装程序将 SWF 安装到用户的计算机时, 它能够安装信任的配置文件并指定 SWF 是受信任的。