---
layout: post
title: MongoDB Community Server 8.0.11在windows下通过zip方式安装
date: 2025-07-02 00:22
author: admin
comments: true
categories: [MongoDB]
tags: [MongoDB]
---

本文主要介绍在Windows系统下通过ZIP文件安装MongoDB Community Server 8.0.11。

<!-- more -->



## 一、安装前准备

1. **确认系统要求**：MongoDB 8.0.11社区版支持64位的Windows 11、Windows 10及Windows Server 2022等系统，需确保系统为64位版本。
2. **下载安装包**：访问MongoDB官方下载页面（<https://www.mongodb.com/try/download/community>），选择版本为8.0.11、平台为Windows x64、安装包类型为zip的MongoDB Community Server安装包进行下载。

## 二、安装步骤

1. **解压安装包**：

	* 将下载的ZIP文件解压到目标目录，例如`D:\dev\database\mongodb-win32-x86_64-windows-8.0.11`。
	* 解压后，确保目录中包含`bin`文件夹，其中包含`mongod.exe`（MongoDB服务端）和`mongosh.exe`（MongoDB Shell，需单独安装）等关键文件。

2. **创建数据目录和日志目录**：

	* 在合适的位置创建数据目录，例如`D:\data\mongodb\db`，用于存储MongoDB的数据文件。
	* 如需记录日志，可创建日志目录，例如`D:\data\mongodb\log`。

3. **配置环境变量（可选）**：

	* 为了方便在命令行中直接运行MongoDB相关命令，可将MongoDB的`bin`目录添加到系统的`PATH`环境变量中。

4. **安装MongoDB Shell（mongosh）**：

	* MongoDB Shell（mongosh）不会随MongoDB Server一起安装，需单独下载并安装。
	* 访问MongoDB官方下载页面（<https://www.mongodb.com/try/download/shell>），下载适用于Windows的mongosh安装包。
	* 运行安装包，按照提示完成安装，并确保在安装过程中将`mongosh.exe`的路径添加到`PATH`环境变量中。

## 三、启动MongoDB服务

1. **以管理员身份打开命令提示符**：


右键点击“命令提示符”或“PowerShell”，选择“以管理员身份运行”。

2. **启动MongoDB服务**：


导航到MongoDB的`bin`目录，例如`cd D:\dev\database\mongodb-win32-x86_64-windows-8.0.11\bin`。运行以下命令启动MongoDB服务，指定数据目录路径：

```bash
mongod.exe --dbpath="D:\data\mongodb\db"
```


如果创建了日志目录，并希望通过配置文件启动服务，可在`bin`目录下创建`mongod.cfg`文件，并添加以下内容：

```yaml
systemLog:
  destination: file
  path: "D:\\data\\mongodb\\log\\mongod.log"
  logAppend: true
storage:
  dbPath: "D:\\data\\mongodb\\db"
net:
  bindIp: 127.0.0.1
  port: 27017
```


然后运行以下命令启动服务：

```bash
mongod.exe --config "D:\dev\database\mongodb-win32-x86_64-windows-8.0.11\bin\mongod.cfg"
```

3. **验证服务是否启动成功**：


如果服务启动成功，可在浏览器中访问`http://127.0.0.1:27017`，如果看到`It looks like you are trying to access MongoDB over HTTP on the native driver port.`的提示，说明服务已成功启动。



## 参考引用


* MongoDB＋Express＋Angular＋Node.js全栈开发实战派(电子工业出版社2020年6月出版)
* Node.js＋Express＋MongoDB＋Vue.js全栈开发实战(清华大学出版社2023年1月出版)
* 循序渐进Node.js企业级开发实践(清华大学出版社2024年12月出版)