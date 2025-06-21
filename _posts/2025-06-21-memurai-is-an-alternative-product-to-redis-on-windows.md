---
layout: post
title: Memurai是Redis on Windows的可替代产品    
date: 2025-06-21 00:22
author: admin
comments: true
categories: [Memurai]
tags: [Memurai,Redis,Windows]
---

经常玩Redis的朋友知道，Redis在Windows上不受官方支持，如果想在Windows环境下安装、使用Redis，除了使用虚拟机之外，就全靠Redis on Windows项目（<https://github.com/microsoftarchive/redis>）了。该项目是微软官方维护，因此采用率很高。但是不幸的是，该项目止步于2016年7月1日，最后支持的Redis的版本为3.0.504。

<!-- more -->

如果你不是为了最求最新的API，则继续使用上述项目也不成问题，毕竟Redis的API版本也是会向前兼容的。但是如果想体验最新的API，则不得不提Redis on Windows项目的继任者——Memurai。


## Memurai简介



Memurai是Redis on Windows项目钦点的替代产品，毕竟在Redis on Windows项目首页是这么描述的：

```
This project is no longer being actively maintained. If you are looking for a Windows version of Redis, you may want to check out Memurai. Please note that Microsoft is not officially endorsing this product in any way.
```


同时Memura也是Redis官方的合作伙伴，Redis官方是这么描述的：


```
Memurai, a division of Janea Systems, is Redis’s official partner for Windows compatibility. Our flagship product, Memurai for Redis, is the only enterprise-ready, Windows-native port of Redis. 
```

Memurai特性如下：

* Compatible with the following features: LRU eviction, persistence, transactions, LUA scripting, high availability, Pub/Sub, cluster, and the Modules API.
* Usage of Memurai Developer Edition in a production environment is prohibited. The Memurai team reserves the right to modify these restrictions at any time.



咱也不知道Memurai和微软之间是否有什么利益关联。反正Memurai也是提供社区版的，既然免费就直接先用起来就完事了。



## 下载安装Memurai


下载地址：<https://www.memurai.com/get-memurai>，选择“Memurai for Redis”版本进行下载，可以得到一个名为“Memurai-for-Redis-v4.2.0.msi”的安装包。以管理员身份双击进行安装。


可以自定义安装目录


![可以自定义安装目录](/images/post/20250621-memurai-001.png)



可以自定义端口

![可以自定义端口](/images/post/20250621-memurai-002.png)



或者也可以用命令行安装：

```
>msiexec /quiet /i Memurai-for-Redis-v4.2.0.msi
```


安装完成之后，在安装目录下可看到如下目录文件：

![在安装目录下可看到如下目录文件](/images/post/20250621-memurai-003.png)


## 配置Memurai

安装完成后，你可以通过编辑Memurai的配置文件来进行一些自定义配置。配置文件位于Memurai安装目录的memurai.conf中。

通过编辑配置文件，你可以配置Memurai的监听地址和端口，设置密码，启用或禁用AOF（append-only file）持久化等。




## 运行Memurai

一旦安装和配置完成，你就可以启动Memurai服务并开始测试运行了。运行 memurai.exe 就可以启动服务器了。

默认情况下，Memurai是已以Windows服务的方式自动启动了，可以请按下`Win + R`组合键打开运行窗口，输入services.msc并点击确定。在服务管理窗口中，找到Memurai服务，对其进行管理，比如启动方式改为手动，将服务关闭、启动等。

启动完成后，打开命令行工具（如Windows PowerShell或cmd），输入memurai-cli命令即可进入Memurai的命令行界面。

在命令行界面中，你可以使用各种Redis命令，如SET、GET、DEL等，与Memurai进行交互。


```
>memurai-cli.exe
127.0.0.1:6379> SET mykey "Hello World!"
OK
127.0.0.1:6379> GET mykey
"Hello World!"
127.0.0.1:6379> DEL mykey
(integer) 1
127.0.0.1:6379>
```