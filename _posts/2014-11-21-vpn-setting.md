---
layout: post
title: 设置 VPN,通过 VPN 翻墙科学上网
date: 2014-11-21 02:41
author: admin
comments: true
categories: [Web]
tags: [Web,VPN]
---

之前介绍了《[用 VPN Gate 翻墙科学上网](http://www.waylau.com/about-vpngate/)》,通过安装客户端，方便实现 VPN 的连接。如果 你爱折腾，也可以手动设置 VPN 。这样的连接 更加稳定。

<!-- more -->

##什么是VPN

虚拟专用网(VPN)被定义为通过一个公用网络(通常是因特网)建立一个临时的、安全的连接，是一条穿过混乱的公用网络的安全、稳定的隧道。虚拟专用网是对企业内部网的扩展。

##VPN 能做啥

* 连接公共 VPN 服务器获得自由访问互联网（翻墙）。
* 绕过您的本地故障防火墙的数据包拦截，并安全地隐藏你的 IP 地址。
* 安全加密网络信号。当你通过互联网发送信息时，保护您的隐私。

##在 windows 上设置 VPN
1.打开网络共享中心

![](http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/vpn/vpn001_zps9fa844e1.jpg)

2.设置新连接

![](http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/vpn/vpn002_zps7054fdfe.jpg)

3.选择 VPN连接

![](http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/vpn/vpn003_zps0af36d2d.jpg)

4.建一个新的连接

![](http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/vpn/vpn004_zps368e912d.jpg)

5.选择使用 VPN

![](http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/vpn/vpn005_zps0631dea5.jpg)

6.输入 VPN 的 IP

![](http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/vpn/vpn006_zpsc9e05e36.jpg)

7.输入 VPN  的账号、密码，最好就记住

![](http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/vpn/vpn007_zpsf1c75c9a.jpg)

8.此时连接可以用了。可以马上连，也可以稍后连接

![](http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/vpn/vpn008_zpsb0c0b05e.jpg)

9.VPN 连接已经创建，右键可以执行“连接”“断开”等操作

![](http://i1288.photobucket.com/albums/b484/waylau/waylau%20blog/vpn/vpn009_zps8af40472.jpg)

##在 Android 上设置 VPN

1. 初始配置 (只需在第一次时安装一次)

从安卓启动 "Settings" 应用程序。
在 "Wireless & Networks" 类别中，打开 "More..." 并点击 "VPN"。
点击 "Add VPN profile" 按钮来创建一个新的 VPN 连接。

![](http://99btgc01.info/uploads/2014/11/001%2812%29.jpg)

一个新的 VPN 连接设置编辑屏幕将出现。在 "Name" 字段输入一些字符串 (如 "vpn" ) ，并在 "Type" 字段选择类型，如 "L2TP/IPSec PSK" 。
在此屏幕上，你必须指定 VPN 主机名或 IP 地址。
复制 DDNS 主机名称 (以 ".opengw.net" 标识符结尾) 或 IP 地址 (位数如:xxx.xxx.xxx.xxx) 并粘贴它到配置屏幕的 "Server address" 字段。

一般情况下，建议指定 DDNS 主机名称 (以 ".opengw.net" 标识符结尾)。DDNS 主机名称可以继续使用，即使 DDNS 主机名对应的 IP 地址将来被改变。然而，在某些国家或地区，您可能无法使用 DDNS 主机名称。如果你不能指定 DDNS 主机名称，尝试使用 IP 地址 (位数如 xxx.xxx.xxx.xxx) 规格来代替。

![](http://99btgc01.info/uploads/2014/11/002l.jpg)

向下滚动配置屏幕，并点击 "Show advanced options" 复选框，如果合适的话。
在 "IPSec pre-shared key" 字段输入 vpn  的密码，
在 "Forwarding routes" 字段输入 "0.0.0.0/0" (9 个字母)。请确定你在 "Forwarding routes" 字段输入正确。如果不对，你不能通过 VPN 通信。
所有输完后，点击 "Save" 按钮并保存 VPN 连接设置。

![](http://99btgc01.info/uploads/2014/11/002l.jpg)

2. 连接 VPN

在任何时候通过使用已创建的 VPN 连接设置，您可以开始一个 VPN 连接。打开 VPN 连接设置列表并点击设置，你将看到以下屏幕。

![](http://99btgc01.info/uploads/2014/11/004%284%29.jpg)

在第一次使用时，你必须输入"Username" 和 "Password" 字段。
然后选择 "Save account information"。
点击 "Connect" 开始 VPN 连接。

![](http://99btgc01.info/uploads/2014/11/005%283%29.jpg)

VPN 连接建立后，指示字符串 "Connected" 将显示在 VPN 连接设置旁，安卓的状态指示区将显示 "VPN activated" 消息。您可以点击消息看当前 VPN 连接状态。
 
3. 通过 VPN 中继享受互联网

当 VPN 建立时，所有到互联网的通讯将通过 VPN 服务器转发。
如果你连接到一个位于海外国家的 VPN 服务器，当你的 VPN 连接建立时，享受 YouTube、Facebook 或 Twitter 吧。

![](http://99btgc01.info/uploads/2014/11/006%283%29.jpg)

##使用免费的 VPN

这里提供可使用的免费的 VPN，[https://github.com/waylau/free-vpn](https://github.com/waylau/free-vpn) 
