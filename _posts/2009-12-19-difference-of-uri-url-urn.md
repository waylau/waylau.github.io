---
layout: post
title: URI、URL、URN 的联系和区别
date: 2009-12-19 00:22
author: admin
comments: true
categories: [Web]
tags: [Web,URI,URL,URN]
---

在 Web 应用中，URI、URL 经常被人混淆和无解。本文详细描述了  URI、URL 以及 URN 之间的联系和区别。
   
<!-- more -->

## 定义

先看定义

* URI（Uniform Resource Identifier，统一资源标识符)是一个用于标识某一互联网资源名称的字符串。 该种标识允许用户对网络中（一般指万维网）的资源通过特定的协议进行交互操作。
* URL(Uniform Resource Locator，统一资源定位符），最常见的形式是 URI,经常指定为非正式的网址。
* URN（Uniform Resource Name，统一资源名称），其目的是通过提供一种途径，用于在特定的命名空间资源的标识，以补充网址。

## 三者关系

![](/images/post/20091219-uri.jpg)

URI 可以进一步划分为定位符（URL），名称（URN）或两者兼备。URL 和 URN 都是 URI 子集。URN 如同一个人的名称，而 URL 代表一个人的住址。换言之，URN 定义某事物的身份，而 URL 提供查找该事物的方法。

用于标识唯一书目的 [ISBN](https://zh.wikipedia.org/wiki/ISBN) 系统是一个典型的 URN 使用范例。例如，ISBN 0-486-27557-4( `urn:isbn:0-486-27557-4` )无二义性地标识出莎士比亚的戏剧《罗密欧与朱丽叶》的某一特定版本。为获得该资源并阅读该书，人们需要它的位置，也就是一个 URL 地址。在类 Unix 操作系统中，一个典型的 URL 地址可能是一个文件目录，例如 `file:///home/username/RomeoAndJuliet.pdf`。该 URL 标识出存储于本地硬盘中的电子书文件。因此，URL 和 URN 有着互补的作用。

URL 是一种 URI，它标识一个互联网资源，并指定对其进行操作或获取该资源的方法。可能通过对主要访问手段的描述，也可能通过网络“位置”进行标识。例如，<http://www.waylau.com> 这个 URL，标识一个特定资源（首页）并表示该资源的某种形式（例如以编码字符表示的，首页的 HTML 代码）是可以通过 HTTP 协议从`www.waylau.com` 这个网络主机获得的。URN 是基于某命名空间通过名称指定资源的URI。人们可以通过 URN 来指出某个资源，而无需指出其位置和获得方式。资源无需是基于互联网的。例如，URN `urn:isbn:0-395-36341-1` 指定标识系统（即国际标准书号ISBN）和某资源在该系统中的唯一表示的 URI。它可以允许人们在不指出其位置和获得方式的情况下谈论这本书。

术语“网址”（没有正式定义）在非技术文献中时常作为 URL 或 URI 的同义词出现，虽然往往其指代的只是“http”和“https”协议。

## 示例

下面是一些 URL 的例子

* ftp://ftp.is.co.za/rfc/rfc1808.txt 
* http://www.ietf.org/rfc/rfc2396.txt 
* ldap://[2001:db8::7]/c=GB?objectClass?one
* mailto:John.Doe@example.com 
* news:comp.infosystems.www.servers.unix 
* tel:+1-816-555-1212
* telnet://192.0.2.16:80/ 
* urn:oasis:names:specification:docbook:dtd:xml:4.1.2
* /other/link.html （相对的 URL,只在另外一个 URL 的上下文有用）

下面是一些 URN 的例子

* urn:isbn:0451450523 （其ISBN编号，以确定一本书）
* urn:uuid:6e8bc430-9c3a-11d9-9669-0800200c9a66 （一个全局唯一标识符）
* urn:publishing:book （标识文档作为一个图书类型的 XML 命名空间）

## 参考：

* <https://www.rfc-editor.org/rfc/rfc3305.txt>
* <https://tools.ietf.org/html/rfc3986>
* <http://stackoverflow.com/questions/176264/what-is-the-difference-between-a-uri-a-url-and-a-urn>