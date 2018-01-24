---
layout: post
title: 基于 MongoDB 的文件服务器 MongoDB File Server 1.0.0 发布！
date: 2018-01-22 00:22
author: admin
comments: true
categories: [MongoDB,MongoDB File Server]
tags: [MongoDB,MongoDB File Server]
---

欣喜的告诉大家，基于 MongoDB 的文件服务器 MongoDB File Server 1.0.0 发布了！MongoDB File Server 致力于小型文件的存储，比如博客中图片、普通文档等。由于MongoDB 支持多种数据格式的存储，对于二进制的存储自然也是不话下，所以可以很方便的用于存储文件。由于 MongoDB 的 BSON 文档对于数据量大小的限制（每个文档不超过16M），所以本文件服务器主要针对的是小型文件的存储。对于大型文件的存储（比如超过16M），MongoDB 官方已经提供了成熟的产品 GridFS，读者朋友可以自行了解。


<!-- more -->

## 特性

* Easy to use.
* RESTful API.
* Chinese characters friendly.
* ...

## APIs

Here are useful APIs.

* GET  /files/{pageIndex}/{pageSize} : Paging query file list.(分页查询文件列表)
* GET  /files/{id} : Download file.(下载某个文件)
* GET  /view/{id} : View file online.(在线预览某个文件。比如，显示图片)
* POST /upload : Upload file.(上传文件)
* DELETE /{id} : Delete file.(删除文件)


## 使用

两步走：

### 1. Get source

```shell
$ git clone https://github.com/waylau/mongodb-file-server.git
```

### 2. Run

```shell
$ gradlew bootRun
```

访问 <http://localhost:8081>.


## 扩展阅读

本文不会对 MongoDB 的概念、基本用法做过多的介绍，有兴趣的朋友可自行查阅其他文献，比如，笔者所著的《分布式系统常用技术及案例分析》、《Spring Boot 教程》等书，对 MongoDB 方面也有所着墨。

设计思路参见：<https://waylau.com/mogodb-file-server-with-spring-boot>

## 项目主页

GitHub：https://github.com/waylau/mongodb-file-server
码云：https://gitee.com/waylau/mongodb-file-server

## 参考文献

*  https://waylau.com/mogodb-file-server-with-spring-boot
* https://github.com/waylau/distributed-systems-technologies-and-cases-analysis
* https://github.com/waylau/spring-boot-tutorial










