---
layout: post
title: Angular CLI找不到模块"angular-devkit/build-angular"
date: 2019-10-19 00:22
author: admin
comments: true
categories: [Angular CLI,Angular]
tags: [Angular CLI,Angular]
---

[Angular CLI](https://waylau.com/angular-cli-commands/) 是 Angular 客户端命令行工具，提供非常多的命令来简化 Angular 的开发。今天执行“ng serve”命令时，竟然报找不到模块"@angular-devkit/build-angular"的错误。

<!-- more -->


## 问题背景

执行“ng serve”命令时，竟然报找不到模块"@angular-devkit/build-angular"的错误。信息如下：

```
>ng serve
An unhandled exception occurred: Could not find module "@angular-devkit/build-angular" from "D:\\workspaceGithub\\mean-news-ui\\mean-news-ui".
See "C:\Users\LYF\AppData\Local\Temp\ng-FStMRr\angular-errors.log" for further details.
```

## 解决

怀疑是 Angular CLI 与 Angular 应用版本不匹配或者是本地环境有错误引起的。

解决的方案就是卸载 Angular CLI再重新安装，错误就没有了。


### 1. 卸载老版本 Angular CLI

卸载老版本 Angular CLI，命令如下：

```
>npm uninstall -g @angular/cli
removed 244 packages in 20.263s
```
### 2. 验证卸载

执行 Angular CLI验证是否已经卸载成功，命令如下：

```
>ng
'ng' 不是内部或外部命令，也不是可运行的程序
或批处理文件。
```

### 3. 清理缓存（可选）

建议清理下缓存，该步骤是可选的：

```
>npm cache clean --force
npm WARN using --force I sure hope you know what you are doing.
```

### 4. 安装新版本 Angular CLI

安装新版本 Angular CLI，命令如下：

```
>npm install -g @angular/cli
C:\Users\LYF\AppData\Roaming\npm\ng -> C:\Users\LYF\AppData\Roaming\npm\node_modules\@angular\cli\bin\ng

> @angular/cli@8.3.12 postinstall C:\Users\LYF\AppData\Roaming\npm\node_modules\@angular\cli
> node ./bin/postinstall/script.js

+ @angular/cli@8.3.12
added 244 packages from 185 contributors in 63.738s
```


## 参考引用

* 本文同步至: <https://waylau.com/angular-could-not-find-module-build-angular/>
* 完整源码：<https://github.com/waylau/angular-enterprise-application-development-samples>
* Angular CLI 常用命令：<https://waylau.com/angular-cli-commands/>