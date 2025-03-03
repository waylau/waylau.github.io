---
layout: post
title: HarmonyOS 3.1/4项目在DevEco Studio 5.0（HarmonyOS NEXT）版本下使用的问题
date: 2024-11-24 00:22
author: admin
comments: true
categories: [HarmonyOS]
tags: [HarmonyOS, NEXT]
---

有读者在使用《[鸿蒙HarmonyOS应用开发入门](https://waylau.com/about-harmonyos-3-tutorial-book/)》书中的源码时，遇到了问题。本文总结问题的原因及解决方案。

<!-- more -->



有读者在使用《鸿蒙HarmonyOS应用开发入门》书中的源码时，遇到了问题。本文总结问题的原因及解决方案。

### 问题原因

这些问题，本质上是 DevEco Studio 版本不一致导致的。 《鸿蒙HarmonyOS应用开发入门》一书选用的是 DevEco Studio 版本为3.1，而读者使用的是 DevEco Studio 5.0（HarmonyOS NEXT），两者存在兼容性的问题。一些 API 的用法、包的位置发生了更改，甚至有些 API 不再推荐使用做了下线。


### 解决方案



因此，作为稳妥的方式是，将 学校用的是 DevEco Studio 5.0版本，改为 3.1。这样书中的示例可以完美运行。 DevEco Studio 3.1 下载地址为：https://developer.huawei.com/consumer/cn/archive/


另外一种方式是，按照 DevEco Studio 5.0 版本，对书中的示例，进行逐个更改，改为5.0 版本最新用法。但这样做，显然工作量很大，读者可以根据《[HarmonyOS 3.1/4.0应用升级到HarmonyOS NEXT改动点](https://waylau.com/changes-upgrade-from-3-1-4-0-to-harmonyos-next-developer-preview2/)》所列出的整改建议进行整改。


最后一种方案是建议采用《鸿蒙HarmonyOS应用开发入门》的升级版本《鸿蒙之光HarmonyOS NEXT原生应用开发入门》。《鸿蒙之光HarmonyOS NEXT原生应用开发入门》是完全采用DevEco Studio 5.0 版本进行编写的，书中的示例也是能在 DevEco Studio 5.0 版本下运行的。当然，《鸿蒙之光HarmonyOS NEXT原生应用开发入门》对前一版本中的一些不再推荐的API用法示例做了删除。


《鸿蒙之光HarmonyOS NEXT原生应用开发入门》预计2024年12月面世，届时读者可以向出版社索取相关配套资料（包括源码和PPT）， 或前往本书的开源社区（https://github.com/waylau/harmonyos-tutorial/）进行下载。

## 参考资料


* 本文同步至：<https://waylau.com/deveco-studio-5-develop-harmonyos-3/>
* 《跟老卫学HarmonyOS开发》 开源免费教程，<https://github.com/waylau/harmonyos-tutorial>
* 《鸿蒙HarmonyOS手机应用开发实战》（清华大学出版社）
* 《鸿蒙HarmonyOS应用开发从入门到精通战》（北京大学出版社）
* “鸿蒙系统实战短视频App 从0到1掌握HarmonyOS”（<https://coding.imooc.com/class/674.html>）
* 《鸿蒙HarmonyOS应用开发入门》（清华大学出版社）
* “鸿蒙零基础快速实战-仿抖音App开发（ArkTS版）”（<https://coding.imooc.com/class/843.html>）