---
layout: post
title: HarmonayOS通过应用链接拉起指定应用
date: 2025-03-03 00:22
author: admin
comments: true
categories: [HarmonayOS]
tags: [HarmonayOS]
---


本节主要介绍如何通过应用链接跳转的方式拉起指定应用。

<!-- more -->


## 应用链接

应用链接是指可以将用户引导至应用内特定位置或相关网页的URL，常见的格式如下。

```
scheme://host[:port]/path
```


应用链接运作机制如下。

* 目标应用在配置文件中注册自己的URL，并对外提供URL。
* 拉起方应用在跳转接口中传入目标应用的URL等信息。
* 系统接收到URL等相关信息，会寻找对应匹配项，并跳转至目标应用。

## 应用链接分类

按照应用链接的scheme以及校验机制的不同，可以分为Deep Linking与App Linking两种方式。

* Deep Linking：是一种通过链接跳转至应用特定页面的技术，其特点是支持开发者定义任意形式的scheme。由于缺乏域名校验机制，容易被其他应用所仿冒。
* App Linking：其限定了scheme必须为https，同时通过增加域名校验机制，可以从已匹配到的应用中筛选过滤出目标应用，消除应用查询和定位中产生的歧义，直达受信的目标应用。

## 拉起指定应用


接下来演示如何通过应用链接跳转的方式拉起指定应用。


#### 1. 创建目标应用


创建名为“ArkTSDeepLinkingTarget”的目标应用。目标应用是被拉起的应用。

修改index.ets内容如下：


```ts
@Entry
@Component
struct Index {
  @State message: string = '目标应用';

  build() {
    RelativeContainer() {
      Text(this.message)
        .id('HelloWorld')
        .fontSize($r('app.float.page_text_font_size'))
        .fontWeight(FontWeight.Bold)
        .alignRules({
          center: { anchor: '__container__', align: VerticalAlign.Center },
          middle: { anchor: '__container__', align: HorizontalAlign.Center }
        })
    }
    .height('100%')
    .width('100%')
  }
}
```


#### 2. 配置目标应用module.json5文件


为了能够支持被其他应用访问，目标应用需要在module.json5配置文件中配置skills标签。


skills标签下默认包含一个skill对象，用于标识应用入口。应用跳转链接不能在该skill对象中配置，需要创建独立的skill对象。如果存在多个跳转场景，需要在skills标签下创建不同的skill对象，否则会导致配置无法生效。

Deep Linking中的scheme取值支持自定义，可以为任意不包含特殊字符、非ohos开头的字符串。通常不为https、http、file，否则会拉起默认的系统浏览器。

配置如下：


```json
{
  "module": {
    // ...省略非核心内容
    "abilities": [
      {
        // ...省略非核心内容
        "skills": [
          {
            "entities": [
              "entity.system.home"
            ],
            "actions": [
              "action.system.home"
            ]
          },
          // 新增一个skill对象，用于跳转场景。如果存在多个跳转场景，需配置多个skill对象
          {
            "actions": [
              // actions不能为空，actions为空会造成目标方匹配失败。
              "ohos.want.action.viewData"
            ],
            "uris": [
              {
                // scheme必选，可以自定义，以link为例，需要替换为实际的scheme
                "scheme": "link",
                // host必选，配置待匹配的域名
                "host": "waylau.com"
              }
            ]
          }
        ]
      }
    ],
    // ...省略非核心内容
  }
}
```

#### 3. 创建拉起应用

创建名为“ArkTSDeepLinkingStartup”的拉起应用。拉起应用的作用是为了拉起目标应用。





修改index.ets内容如下：


```ts
import { common, OpenLinkOptions } from '@kit.AbilityKit';
import { BusinessError } from '@kit.BasicServicesKit';
import { hilog } from '@kit.PerformanceAnalysisKit';

const TAG: string = '[ArkTSDeepLinkingStartup]';
const DOMAIN_NUMBER: number = 0xFF00;

@Entry
@Component
struct Index {
  @State message: string = '启动目标应用';

  build() {
    RelativeContainer() {
      Text(this.message)
        .id('HelloWorld')
        .fontSize($r('app.float.page_text_font_size'))
        .fontWeight(FontWeight.Bold)
        .alignRules({
          center: { anchor: '__container__', align: VerticalAlign.Center },
          middle: { anchor: '__container__', align: HorizontalAlign.Center }
        })
        .onClick(() => {
          let context: common.UIAbilityContext = getContext(this) as common.UIAbilityContext;
          let link: string = "link://waylau.com";
          let openLinkOptions: OpenLinkOptions = {
            //跳转目标应用（如果有多个符合条件的应用时，展示应用选择弹框）
            appLinkingOnly: false
          };

          try {
            context.openLink(link, openLinkOptions)
              .then(() => {
                hilog.info(DOMAIN_NUMBER, TAG, 'open link success.');
              }).catch((err: BusinessError) => {
              hilog.error(DOMAIN_NUMBER, TAG, `open link failed. Code is ${err.code}, message is ${err.message}`);
            });
          } catch (paramError) {
            hilog.error(DOMAIN_NUMBER, TAG, `Failed to start link. Code is ${paramError.code}, message is ${paramError.message}`);
          }
        })
    }
    .height('100%')
    .width('100%')
  }
}
```

在上述代码中，在openLink接口的link字段中传入目标应用的URL信息，并将options字段中的appLinkingOnly配置为false。



#### 4. 运行应用

在虚拟机上先运行目标应用以后，而后将应用关闭，如图所示。这样就确保目标应用已经在模拟机上安装了。


![确保目标应用已经在模拟机上安装](../images/post/20250303-harmonyos-deep-linking.png)



其次，运行拉起应用，如图所示。点击拉起应用的文本以触发启动目标应用事件。

![运行拉起应用](../images/post/20250303-harmonyos-deep-linking-2.png)


拉起应用的效果如下图所示。

![运行拉起应用](../images/post/20250303-harmonyos-deep-linking-3.png)


## 参考引用

* 原文同步至：<https://waylau.com/harmonyos-open-app-with-deep-linking/>

更多示例源码、HarmonyOS学习资料可见

* 《跟老卫学HarmonyOS开发》 开源免费教程，<https://github.com/waylau/harmonyos-tutorial>
* 《鸿蒙HarmonyOS手机应用开发实战》（清华大学出版社）
* 《鸿蒙HarmonyOS应用开发从入门到精通战》（北京大学出版社）
* 《鸿蒙HarmonyOS应用开发入门》（清华大学出版社）
* “鸿蒙系统实战短视频App 从0到1掌握HarmonyOS”（<https://coding.imooc.com/class/674.html>）
* “鸿蒙零基础快速实战-仿抖音App开发（ArkTS版）”（<https://coding.imooc.com/class/843.html>）
* 《鸿蒙HarmonyOS应用开发从入门到精通（第2版）》（北京大学出版社)
* 《鸿蒙之光HarmonyOS NEXT原生应用开发入门》（清华大学出版社)