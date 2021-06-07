---
layout: post
title: 使用ADB Shell将SD卡转为内部存储
date: 2021-05-31 00:22
author: admin
comments: true
categories: [ADB]
tags: [ADB,Shell]
---

随着手机的使用，内存捉襟见肘。本文介绍如何使用SD卡来扩展内存。

<!-- more -->


## 1. 下载ADB Kits

下载地址为 <https://adbshell.com/downloads>

## 2. 开启USB调试

打开 开发者选项->调试-USB调试，USB连接电脑。

如果手机显示授权信息点击确认。

## 3. 运行ADB Shell

输入

```
adb shell
```


接着输入

```
sm list-disks

```

此时显示的是SD卡的盘号 179,64

设置为内部存储

```
sm partition disk:179,64 private
```

设置为便携式存储

```
sm partition disk:179,64 public
```

混合模式：

```
sm partition disk:179,64 mixed 50  
```


以“sm partition disk:179,64 mixed 70”为例，意思是把sd卡容量的70%设置为便携式存储（存储照片等），30%设置为内部存储（存储软件） 。


## 4. 验证

成功之后，打开 设置->存储设备



## 5. 移动应用到新的SD卡的内部存储

手机设置-其它设置-开发者选项-强制允许将应用写入外部存储 打开

## 6. 缺点

会对SD卡做格式化，预先要备份好卡内数据。
看到新分区的内部存储的情况，被隐藏了。


## 参考引用

* 本文同步至: <https://waylau.com/use-adb-shell-to-convert-sd-card-to-internal-storage/>
* 视频版本，见我的B站：<https://www.bilibili.com/video/BV1XQ4y1X7ti/>