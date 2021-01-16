---
layout: post
title: Oracle VM VirtualBox安装Win10及网络配置
date: 2021-01-15 01:41
author: admin
comments: true
categories: [VirtualBox,Windows]
tags: [VirtualBox,Windows]
---
 
VirtualBox 是一款开源虚拟机软件。本文介绍在安装Windows时，如何配置网络。

<!-- more -->

安装方式见<https://waylau.com/categories/#VirtualBox>


## 问题

按照默认的配置进行了安装，默认情况下，虚拟机可以上网，可以ping通宿主机，但宿主机没法ping通虚拟机。



![](../images/post/20210116-001-virtualbox.png)

## 解决

解决方式是：

1. 启用DHCP服务器：网络地址跟宿主的网段设成一致



![](../images/post/20210116-002-virtualbox.png)

2. 连接方式：桥接网卡


![](../images/post/20210116-003-virtualbox.png)

## 结果


设置完成之后，可以看到宿主和虚拟机在同一个网段了，都可以上网，都可以相互ping通。



![](../images/post/20210116-004-virtualbox.png)