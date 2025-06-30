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


## 使用MongoDB Shell 2.5.3

在Windows系统下通过ZIP文件安装MongoDB Shell 2.5.3，可按照以下步骤进行：

### 下载MongoDB Shell 2.5.3 ZIP文件

1. 访问MongoDB官方下载页面：https://www.mongodb.com/try/download/shell。
2. 在下载页面中，选择适合Windows系统的版本，并确保版本号为2.5.3（或最新稳定版，如果2.5.3不可用）。
3. 选择ZIP格式作为安装包类型，然后点击“Download”按钮开始下载。

### 解压安装包

1. 下载完成后，选择一个合适的目录来解压MongoDB Shell ZIP文件，例如`D:\dev\database\mongosh-2.5.3-win32-x64`。
2. 右键点击下载的ZIP文件，选择“解压到当前文件夹”或使用解压工具将其解压到指定目录。

### 配置环境变量（可选）

为了方便在命令行中直接运行MongoDB Shell命令，可将MongoDB Shell的`bin`目录添加到系统的`PATH`环境变量中。

1. 右键点击“此电脑”或“我的电脑”，选择“属性”。
2. 点击“高级系统设置”，然后点击“环境变量”。
3. 在“系统变量”部分，找到并选择`Path`变量，然后点击“编辑”。
4. 在“编辑环境变量”窗口中，点击“新建”，然后输入MongoDB Shell的`bin`目录路径，例如`D:\dev\database\mongosh-2.5.3-win32-x64\bin`。
5. 点击“确定”保存更改。

### 验证安装

1. 打开命令提示符（CMD）或PowerShell。
2. 输入`mongosh --version`命令，然后按Enter键。
3. 如果安装成功，将显示MongoDB Shell的版本信息，其中应包含2.5.3或您安装的版本号。

### 使用MongoDB Shell

1. 在命令提示符或PowerShell中，输入`mongosh`命令，然后按Enter键。
2. 如果MongoDB服务器运行在本地默认端口（27017），MongoDB Shell将自动连接到该服务器。输出内容如下

```
>mongosh
Current Mongosh Log ID: 6862ae96944d498931748a5e
Connecting to:          mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.5.3
Using MongoDB:          8.0.11
Using Mongosh:          2.5.3

For mongosh info see: https://www.mongodb.com/docs/mongodb-shell/


To help improve our products, anonymous usage data is collected and sent to MongoDB periodically (https://www.mongodb.com/legal/privacy-policy).
You can opt-out by running the disableTelemetry() command.

------
   The server generated these startup warnings when booting
   2025-06-30T17:37:01.619+08:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
------

test> 
```

连接成功后，可以执行各种MongoDB数据库操作。


#### 显示已有的数据库





使用“db”命令，可以显示当前使用的数据库：


```
test> db
test
```

在MongoDB新建时，默认会有一个test数据库。



使用“show dbs”命令，可以显示已有的数据库：

```
test> show dbs
admin   40.00 KiB
config  12.00 KiB
local   40.00 KiB
```

#### 创建、使用数据库


“use”命令有两个作用：

* 切换到指定的数据库；
* 在数据库不存在时，创建数据库。

因此，可以通过下面的命令来创建并使用数据库：


```
test> use rednote
switched to db rednote
rednote>
```


## 参考引用


* MongoDB＋Express＋Angular＋Node.js全栈开发实战派(电子工业出版社2020年6月出版)
* Node.js＋Express＋MongoDB＋Vue.js全栈开发实战(清华大学出版社2023年1月出版)
* 循序渐进Node.js企业级开发实践(清华大学出版社2024年12月出版)