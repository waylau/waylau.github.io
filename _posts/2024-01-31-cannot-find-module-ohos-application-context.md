---
layout: post
title: HarmonyOS开发找不到模块“@ohos.application.context”
date: 2024-01-31 00:22
author: admin
comments: true
categories: [HarmonyOS]
tags: [HarmonyOS]
---


打开2023年创建的一个历史的版本的HarmonyOS应用时报错：`Cannot find module '@ohos.application.context' or its corresponding type declarations. <ArkTSCheck>`。

<!-- more -->

## 问题原因

应该是HarmonyOS SDK版本升级了，所以原有的模块名称做了变更。估计是OpenHarmony SDK从3.1更新到3.2导致的。

## 解决方法

### 方法1：降版本

DevEco Studio版本降低，或者OpenHarmony SDK版本降低到3.1。

### 方法2：使用最新SDK对应的模块

将`@ohos.application.context`模块改为`'@ohos.app.ability.common`。老版用法如下：

```ts
import context from '@ohos.application.context';

let context = getContext(this) as context.AbilityContext;
```


新版用法：

```ts
import common from '@ohos.app.ability.common';

let context = getContext(this) as common.UIAbilityContext;
```

最好是从新创建应用，因为新版的配置和老版的配置差异还是比较大。新创建应用就可以不用管配置上的差异了。