---
layout: post
title: 一文搞懂什么是鸿蒙、OpenHarmony、HarmonyOS
date: 2021-07-05 00:22
author: admin
comments: true
categories: [HarmonyOS]
tags: [HarmonyOS,OpenHarmony]
---

很多人对鸿蒙、OpenHarmony、HarmonyOS这些术语傻傻的分不清楚，那么本文就做一些解答。

<!-- more -->


6月4日，华为发布了《关于规范HarmonyOS沟通口径的通知》（以下简称《通知》）。本人对该通知做一些解读如下。

## 《通知》背景

《通知》开篇就说“关于‘鸿蒙操作系统’，由于缺乏规范的表述和统一的口径，导致内部理解不一致、对外说法不一致，容易引起混淆”所以，大家不要不好意思啊，不光你不懂，很多人都傻傻的分不清。

不懂不要怕，咱们继续往下看。

## 鸿蒙操作系统

“鸿蒙操作系统”特指华为智能终端操作系统。“鸿蒙操作系统”具有以下特征：

* 一是一套操作系统可以满足大大小小设备需求，实现统一OS，弹性部署；
* 二是搭载该操作系统的设备在系统层面融为一体、形成超级终端，让设备的硬件能力可以弹性扩展，实现设备之间硬件互助，资源共享；
* 三是面向开发者，实现一次开发，多端部署。

## OpenHarmony

OpenHarmony是一个开源项目，由开放原子开源基金会（<https://www.openatom.org/>）进行管理。开放原子开源基金会由华为、阿里、腾讯、百度、浪潮、招商银行、360等十家互联网企业共同发起组建。

OpenHarmony暂时还没有中文名字，名字还在申请中。项目地址为：<https://gitee.com/openharmony>

OpenHarmony开源项目主要包括两部分：

* 一是华为捐献的“鸿蒙操作系统”的基础能力；
* 二是其他参与者的贡献。

因此，OpenHarmony是“鸿蒙操作系统”的底座。


## HarmonyOS

HarmonyOS就是“鸿蒙操作系统”，或者简称为“鸿蒙OS”是基于 OpenHarmony、AOSP等开源项目的商用版本。

这里需要注意：

* 一是HarmonyOS不是开源项目，而是商用版本。
* 二是HarmonyOS手机和平板之所以能运行Android，是因为HarmonyOS 实现了现有Android生态应用（即AOSP）的运行。

## 鸿蒙生态

鸿蒙生态包括 OpenHarmony 和 HarmonyOS，当然还包括开发工具以周边的一些开发库。当我们在说“鸿蒙”的时候，也许就是指鸿蒙生态。


![](https://communityfile-drcn.op.hicloud.com/FileServer/getFile/cmtybbs/042/413/002/0000000000042413002.20210705171347.24388496837799371154469482437918:50520705141008:2800:CA9364131A8231DB3BFB1A168B307F02F3C17392E7B17396193391011872E313.png)

## 何时选择OpenHarmony或是HarmonyOS？

如果你只是一个应用开发工程师，专注于终端设备的应用开发，那么选择HarmonyOS。这里也有免费开源教程《[跟老卫学HarmonyOS开发](https://github.com/waylau/harmonyos-tutorial)》。

如果你对HarmonyOS底层的技术感兴趣，想了解或者想对HarmonyOS做贡献，那么选择OpenHarmony。当然，如果想更进一步，做一款属于自己的操作系统，基于OpenHarmony开源项目做二次开发也是不错的选择哦。

以上就是本人对于鸿蒙相关术语的一些解读，看我理解的对不对呢？欢迎大家讨论。

## 参考引用

* 本文同步至: <https://waylau.com/what-is-harmonyos/>
* 《跟老卫学HarmonyOS开发》：<https://github.com/waylau/harmonyos-tutorial>
* 《关于规范HarmonyOS沟通口径的通知》：<https://xinsheng.huawei.com/cn/index.php?app=forum&mod=Detail&act=index&id=5805323>
