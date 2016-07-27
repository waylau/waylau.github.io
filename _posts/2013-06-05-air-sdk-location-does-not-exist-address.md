---
layout: post
title:   AIR SDK 0 AIR SDK location does not exist 解决
date:   2013-06-05 01:58
author: admin
comments: true
categories: [AIR]
tags: [AIR]
---

导入AS3项目时提示	`“AIR SDK 0.0: AIR SDK location "D:\Program Files\Adobe Flash Builder 4.7\eclipse\plugins\com.adobe.flexbuilder.flex_4.7.0.349722\devsdks\AIRSDK\Win" does not exist.”`	

是AS3项目找不见AIR SDK. 打开项目配置 `ActionScript Build Path` ，路径出错。

他默认是去Flash Builder 4.7安装目录下的`..\Adobe Flash Builder 4.7\eclipse\plugins\com.adobe.flexbuilder.flex_4.7.0.349722\devsdks\AIRSDK\Win`去找。

但是 我的Builder根本没有这个Win文件

##解决步骤：

###1. 先去官网下载下载和更新SDK

可参考此文<http://www.waylau.com/update-air-sdk-flash-builder>

###2. 如果上述方法无效

在`..\Adobe Flash Builder 4.7\eclipse\plugins\com.adobe.flexbuilder.flex_4.7.0.349722\ `目录下创建 devsdks文件夹，再在devsdks创建AIRSDK，再在AIRSDK下创建Win 即创建它搜索的那个路径

###3.将下载的AIRSDK中的文件复制进新建的Win文件夹下。

###4.回到配置界面ActionScript Build Path移除之前的AIRSDK,再点击“ADD AIR SDK”