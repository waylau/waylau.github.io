---
layout: post
title: Webmin 安装和使用
date: 2015-05-29 01:41
author: admin
comments: true
categories: [Webmin]
tags: [Webmin]
---
 
Webmin 是目前功能最强大的基于 Web 的 Unix 系统管理工具。管理员通过浏览器访问Webmin 的各种管理功能并完成相应的管理动作。目前 Webmin 支持绝大多数的Unix系统，这些系统除了各种版本的 linux 以外还包括：AIX、HPUX、Solaris、Unixware、Irix 和 FreeBSD 等。

<!-- more -->

##	环境

* CentOS 7.1 (64-bit system)
* Webmin 1.750 

## 下载

下载 Webmin 执行 

    wget http://prdownloads.sourceforge.net/webadmin/webmin-1.740-1.noarch.rpm

## 安装

采用 yum 安装，执行

    yum localinstall webmin-1.740-1.noarch.rpm

## 设置防火墙

开发 10000 号端口

    firewall-cmd --zone=public --add-port=10000/tcp --permanent
    
重启防火墙    
    
    firewall-cmd --reload

## 访问

浏览器可以远程访问 `http://[主机IP]:10000`

![](http://99btgc01.info/uploads/2015/05/001%282%29.png)

登录用户名密码为 操作系统的用户名、密码

![](http://99btgc01.info/uploads/2015/05/002%282%29.png)

![](http://99btgc01.info/uploads/2015/05/003%281%29.png)

参考<http://doxfer.webmin.com/Webmin/Main_Page>