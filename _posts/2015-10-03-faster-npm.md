---
layout: post
title: 加速 npm
date: 2015-10-03 01:08
author: admin
comments: true
categories: [Node.js]
tags: [Node.js,npm]
---

## 问题

[Node.js](https://nodejs.org) 的依赖包管理生态系统 [npm](https://www.npmjs.com/), 是世界上最大的生态系统开源库。
但国内使用 npm 来安装软件，速度很慢，有时候甚至直接就失败了！ 

![](http://99btgc01.info/uploads/2015/10/nodejs.jpg)

<!-- more -->

## 原因

npm 默认是从国外的源获取和下载包信息，不慢才怪，有时甚至被墙，导致无法正常安装软件。

## 解决

可以采用国内的 npm 镜像来解决网速慢的问题。 

这里以“淘宝 NPM 镜像”举例。淘宝 NPM 镜像这是一个完整 npmjs.org 镜像，你可以用此代替官方版本(只读)，同步频率目前为 10分钟 一次以保证尽量与官方服务同步，镜像地址为  registry.npm.taobao.org， 是从 registry.npmjs.org 进行全量同步的。

### 方法1：使用 --registry

在安装软件时，使用 --registry 来注册镜像地址到国内的镜像

如：

    npm install gitbook-cli -g --registry=http://registry.npm.taobao.org

这样，安装软件速度会很快哦。

![](http://99btgc01.info/uploads/2015/10/nodejs2.jpg)

### 方法2：设置 registry

方法1 是每次使用都需要注册镜像源，未免繁琐。设置国内的镜像为默认镜像源，则更为方便：

    npm config set registry=http://registry.npm.taobao.org

### 方法3：使用 cnpm

[cnpm](https://github.com/cnpm/cnpm) 是 npm 中国镜像的 npm 客户端，可以代替 npm。

先用 npm 安装 cnpm：

    npm install -g cnpm --registry=https://registry.npm.taobao.org

![](http://99btgc01.info/uploads/2015/10/nodejs3.jpg)

而后，安装软件就能直接用 cnpm 代替 npm 了：

    cnpm install [name]
    
## 参考

* <http://npm.taobao.org/>
* <https://nodejs.org/docs/latest-v0.12.x/api/>