---
layout: post
title: 简单实现浏览国外著名网站的方法，不用梯子，修改本机的 hosts
date: 2011-04-28 00:26
author: admin
comments: true
categories: [Web]
tags:  [Web,hosts]
---
修改本机的 hosts 文件

一般的

hosts文件在不同操作系统（甚至不同Windows版本）的位置都不大一样：
Windows NT/2000/XP/Vista/7/8（即微软NT系列操作系统）：默认位置为%SystemRoot%\system32\drivers\etc\，但也可以改变。动态目录由注册表键\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\DataBasePath决定。
Windows 95/98/Me：%WinDir%\
Linux及其他类Unix操作系统：/etc
Mac OS 9及更早的系统：System Folder: Preferences或System folder（文件格式可能与Windows和Linux所对应的文件不同）
Mac OS X：/private/etc（使用BSD风格的hosts文件）
OS/2及eComStation："bootdrive":\mptn\etc\
Android：/system/etc/hosts
Symbian第1/2版手机：C:\system\data\hosts
Symbian第3版手机：C:\private\10000882\hosts，只能使用兼容AllFiles的文件浏览器访问，大部分都不行[1]。
iOS（iPhone）：/etc
iOS（iPad）：/private/etc
webOS:/etc

文件中添加要访问的IP 和 域名即可

	127.0.0.1 localhost
	127.0.0.1 activate.adobe.com
	74.125.224.231 drive.google.com
	203.208.46.206 m.youtube.com
	203.208.46.206 s.ytimg.com
	203.208.46.206 www.youtube.com

hosts文件也可以用于其它情况，例如可以将已知的广告服务器重定向到无广告的机器（通常是本地的IP地址：127.0.0.1）上来过滤广告。同时也可以通过不下载网络广告，从而减少带宽。使用hosts文件还可以减少对DNS服务器的访问来加快访问速度并减少带宽消耗。
hosts文件的另一个重要用途就是用于拦截一些恶意网站的请求，从而防止访问欺诈网站或感染一些病毒或恶意软件。但同时，这个文件也可能被病毒或恶意软件所利用来阻止用户更新杀毒软件或访问特定网站。

文章参考：<http://zh.wikipedia.org/wiki/Hosts%E6%96%87%E4%BB%B6>

更多参数看考：<http://code.google.com/p/smarthosts/>

<http://code.google.com/p/huhamhire-hosts/>
 
