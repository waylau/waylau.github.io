---
layout: post
title: HarmonyOS通过openLink拉起浏览器打开链接
date: 2025-11-05 00:22
author: admin
comments: true
categories: [HarmonyOS]
tags: [HarmonyOS]
---

本文介绍了在HarmonyOS应用开发过程中，如何使用openLink拉起浏览器来打开链接。
<!-- more -->


## App Linking Kit简介

HarmonyOS的App Linking Kit（应用链接服务）提供了一系列增强的链接特性。App Linking Kit支持通过基础链接拉起指定应用，实现应用间跳转。当应用已安装时，优先通过应用展示内容；若应用未安装，则通过系统浏览器展示网页版内容。


使用App Linking进行跳转时，系统会根据接口传入的uri信息（HTTPS链接）将用户引导至目标应用中的特定内容，无论应用是否已安装，用户都可以访问到链接对应的内容，跳转体验相比Deep Linking方式更加顺畅。


## 拉起方应用跳转实现


拉起方应用通过UIAbilityContext.openLink()接口，传入目标应用的链接，拉起目标应用。

openLink接口提供了两种拉起目标应用的方式，开发者可根据业务需求进行选择。

### 方式一： 仅以App Linking的方式打开应用。

将appLinkingOnly参数设为true，若有App Linking匹配的应用，则直接打开目标应用。若无App Linking匹配的应用，则抛异常给开发者进行处理。

适用于无法打开目标应用时，开发者做了相应的异常处理。例如：拉起方应用集成了ArkWeb，当目标应用不存在时，可通过ArkWeb打开链接。

### 方式二： 以App Linking优先的方式打开应用。


将appLinkingOnly参数设为false或者不传，若有App Linking匹配的应用，则直接打开目标应用。若无App Linking匹配的应用，则尝试以浏览器打开链接的方式打开应用。

适用于无法打开目标应用时，开发者未做任何处理。此时目标应用不存在时，会通过系统浏览器打开链接。


### 代码示例

本文主要验证上述方式二，核心代码逻辑如下。


```ts
// 获取上下文
let context = this.getUIContext().getHostContext() as common.UIAbilityContext;
let link: string = this.url;
let openLinkOptions: OpenLinkOptions = {
  // 将appLinkingOnly参数设为false或者不传，
  // 若有App Linking匹配的应用，则直接打开目标应用。
  // 若无App Linking匹配的应用，则尝试以浏览器打开链接的方式打开应用
  appLinkingOnly: false,
  parameters: {}
};

try {
  context.openLink(
    link,
    openLinkOptions,
    (err, result) => {
      hilog.error(DOMAIN, TAG, `openLink callback error.code: ${JSON.stringify(err)}`);
      hilog.info(DOMAIN, TAG, `openLink callback result: ${JSON.stringify(result.resultCode)}`);
      hilog.info(DOMAIN, TAG, `openLink callback result data: ${JSON.stringify(result.want)}`);
    }
  ).then(() => {
    hilog.info(DOMAIN, TAG, `open link success.`);
  }).catch((err: BusinessError) => {
    hilog.error(DOMAIN, TAG, `open link failed, errCode ${JSON.stringify(err.code)}`);
  });
} catch (e) {
  hilog.error(DOMAIN, TAG, `exception occured, errCode ${JSON.stringify(e.code)}`);
}
```


运行应用，界面效果如下。



![](/images/post/20251105-harmonyos6-001.jpg)



点击应用“访问链接”，可以看到拉起了浏览器，界面效果如下。


![](/images/post/20251105-harmonyos6-002.jpg)



在浏览器里面访问被访问的应用，界面效果如下。

![](/images/post/20251105-harmonyos6-003.jpg)




完整示例详见文末链接《跟老卫学仓颉编程语言开发》中“ArkTSOpenLink”。


## 参考引用

加入鸿蒙生态，共建万物互联。以下是鸿蒙应用开发常用教程。



* 本文同步至：<https://waylau.com/harmonyos-launches-a-browser-to-open-a-link-through-openlink/>
* 《跟老卫学HarmonyOS开发》开源免费教程， <https://github.com/waylau/harmonyos-tutorial>
* 《跟老卫学AI大模型开发》开源免费教程， https://github.com/waylau/ai-large-model-tutorial/
* 《跟老卫学仓颉编程语言开发》开源免费教程， https://github.com/waylau/cangjie-programming-language-tutorial
* 《鸿蒙HarmonyOS手机应用开发实战》（清华大学出版社）
* 《鸿蒙HarmonyOS应用开发入门》（清华大学出版社）
* “鸿蒙零基础快速实战-仿抖音App开发（ArkTS版）”（https://coding.imooc.com/class/843.html）
* 《鸿蒙HarmonyOS应用开发从入门到精通（第2版）》（北京大学出版社)
* 《鸿蒙之光HarmonyOS NEXT原生应用开发入门》（清华大学出版社)
* “HarmonyOS NEXT+AI大模型打造智能助手APP(仓颉版)”（https://coding.imooc.com/class/927.html）
* “HarmonyOS 6 AI应用开发”(https://edu.51cto.com/course/39601.html)