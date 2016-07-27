---
layout: post
title: Windows 下自动化更新 hosts
date: 2015-11-21 01:08
author: admin
comments: true
categories: [Web]
tags: [Windows,hosts,翻墙]
---

鉴于国内的网络环境，有时候，我们需要修改 hosts 文件来实现访问国外著名网站的目的（翻墙）。本文介绍了在 Windows 下自动化更新 hosts 方法。

<!-- more -->

## hosts 简介 

有关 hosts 的作用和修改 hosts 文件实现翻墙的原理，可以参见<http://www.waylau.com/simple-way-to-implement-browsing-famous-overseas-web-sites-without-the-ladder-gateway-dns-hosts/>。

### hosts 源

hosts 源是指包含可用 Google 、YouTube、Google+、Facebook、Twitter、维基百科、App Store、Dropbox 等著名网站 IP 的 hosts 文件。有些网站专门会定期更新此类 hosts 源，比如：<https://github.com/racaljk/hosts>

## 环境

* Windows 7 : 64 位
* cURL :  版本 7.33 

## 安装 cURL

### 下载 cURL 

本例版本为： <http://curl.haxx.se/download/curl-7.33.0-win64-ssl-sspi.zip>

### 安装 cURL

解压下载包，将包内的 `curl.exe` 放到 `C:\Windows\System32` 目录。

### 验证

命令行下，执行 

```
curl -V
```

看到版本号，则说明安装成功

![](http://99btgc01.info/uploads/2015/11/hosts1.jpg)

## 制作批处理文件

新建一个 .bat 文件, 复制下面的内容到该文件

```
curl  -k -o hosts  https://raw.githubusercontent.com/racaljk/hosts/master/hosts
move /Y hosts C:\Windows\System32\drivers\etc\hosts 
ipconfig /flushdns
```

双击 .bat 文件，则会自动执行 hosts 更新。

### 原理

* 第1句是指，使用 curl 将 url 的资源下载到本地命名为 hosts 的文件中。 `-k` 是忽略 SSL 验证的意思；
* 第2句是指，将下载的 hosts 覆盖操作系统自带的 hosts
* 第3句是指，刷新网络设置

![](http://99btgc01.info/uploads/2015/11/hosts.jpg)

## 其他翻墙方式

* [简单实现浏览国外著名网站的方法，不用梯子，修改本机的 hosts](http://www.waylau.com/simple-way-to-implement-browsing-famous-overseas-web-sites-without-the-ladder-gateway-dns-hosts/)
* [用Heroku、snova-c4搭梯子上墙](http://www.waylau.com/heroku-snova-c4-cross-wall/)
* [又一架梯子上墙利器--DNSCrypt](http://www.waylau.com/about-dnscrypt/)
* [用 VPN Gate 翻墙科学上网](http://www.waylau.com/about-vpngate/)
* [设置 VPN,通过 VPN 翻墙科学上网](http://www.waylau.com/vpn-setting/)
* [用 Azure RemoteApp 翻墙科学上网](http://www.waylau.com/about-azure-remoteapp/)

## 参考

* [给程序员的开源免费图书集合](https://github.com/waylau/books-collection)