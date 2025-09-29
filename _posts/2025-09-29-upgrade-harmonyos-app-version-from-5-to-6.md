---
layout: post
title: 如何将HarmonyOS 5应用升级到HarmonyOS 6
date: 2025-09-29 00:22
author: admin
comments: true
categories: [HarmonyOS]
tags: [HarmonyOS]
---

DevEco Studio 6.0.0 Release（6.0.0.858）已经于2025年9月25日正式发布，标志着以API 20为核心的HarmonyOS全套开发套件（含SDK及开发工具DevEco Studio）均达到Release状态并正式发布。开发者可基于Release状态的开发套件进行HarmonyOS 6应用开发。本文主要介绍如何将HarmonyOS 5应用升级到HarmonyOS 6。

<!-- more -->

## 设置targetSdkVersion
 
用最新的DevEco Studio，打开HarmonyOS 5应用，会出现如下提示：

![](/images/post/20250929-harmonyos-update.png)

点第二项。此时，build-profile.json5会自动添加targetSdkVersion配置项，改为了如下：

```json
{
  "app": {
    "signingConfigs": [],
    "products": [
      {
        "name": "default",
        "signingConfig": "default",
        "compatibleSdkVersion": "5.0.0(12)",
        "runtimeOS": "HarmonyOS",
        "buildOption": {
          "strictMode": {
            "caseSensitiveCheck": true
          }
        },
        "targetSdkVersion": "6.0.0(20)"
      }
    ],
    "buildModeSet": [
      {
        "name": "debug",
      },
      {
        "name": "release"
      }
    ]
  },
  "modules": [
    {
      "name": "entry",
      "srcPath": "./entry",
      "targets": [
        {
          "name": "default",
          "applyToProducts": [
            "default"
          ]
        }
      ]
    }
  ]
}
```


 

## 升级modelVersion

打开Migrate Assistant，



![](/images/post/20250929-harmonyos-migrate-assistant.png)



会自动做如下更改：

To start migration, select the version to which you want to migrate this project (in both hvigor and ohpm) and click Migrate.

This migration will lead to the following changes:
1. Upgrade hvigor modelVersion from 5.0.0 to 6.0.0
2. Upgrade ohpm modelVersion from 5.0.0 to 6.0.0




![](/images/post/20250929-harmonyos-migrate.png)




点击Migrate

oh-package.json5会自动改为了如下：



```json
{
  "modelVersion": "6.0.0",
  "description": "Please describe the basic information.",
  "dependencies": {
  },
  "devDependencies": {
    "@ohos/hypium": "1.0.19",
    "@ohos/hamock": "1.0.0"
  }
}
```

## 代码整改


查看编译情况，关注控制台日志。如果有报错，根据提示进行整改。

常见的问题是，鸿蒙6的ArkTS语言语法规则更加严格，
因此，需要做一些判空处理或者是类型指定等。

## 视频演示

详见B站：https://www.bilibili.com/video/BV1yBHwzDEkK/



## 参考引用



* 本文同步至：<https://waylau.com/upgrade-harmonyos-app-version-from-5-to-6/>
* 源码见《跟老卫学HarmonyOS开发》开源免费教程， <https://github.com/waylau/harmonyos-tutorial>
* 《跟老卫学AI大模型开发》开源免费教程， https://github.com/waylau/ai-large-model-tutorial/
* 《跟老卫学仓颉编程语言开发》开源免费教程， https://github.com/waylau/cangjie-programming-language-tutorial
* 《鸿蒙HarmonyOS手机应用开发实战》（清华大学出版社）
* 《鸿蒙HarmonyOS应用开发入门》（清华大学出版社）
* “鸿蒙零基础快速实战-仿抖音App开发（ArkTS版）”（https://coding.imooc.com/class/843.html）
* 《鸿蒙HarmonyOS应用开发从入门到精通（第2版）》（北京大学出版社)
* 《鸿蒙之光HarmonyOS NEXT原生应用开发入门》（清华大学出版社)
* “HarmonyOS NEXT+AI大模型打造智能助手APP(仓颉版)”（https://coding.imooc.com/class/927.html）
