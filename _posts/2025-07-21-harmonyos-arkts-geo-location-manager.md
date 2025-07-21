---
layout: post
title: HarmonyOS ArkTS 获取位置服务
date: 2025-07-21 00:22
author: admin
comments: true
categories: [Nacos]
tags: [Nacos]
---

本文主要介绍在HarmonyOS下如何使用ArkTS获取位置服务。

<!-- more -->

## Location Kit概述

移动终端设备已经深入人们日常生活的方方面面，如查看所在城市的天气、新闻轶事、出行打车、旅行导航、运动记录。这些习以为常的活动，都离不开定位用户终端设备的位置。

当用户处于这些丰富的使用场景中时，系统的位置能力可以提供实时准确的位置数据。对于开发者，设计基于位置体验的服务，也可以使应用的使用体验更贴近每个用户。

当应用在实现基于设备位置的功能时，如：驾车导航，记录运动轨迹等，可以调用该模块的API接口，完成位置信息的获取。

Location Kit（位置子系统）使用多种定位技术提供服务，如GNSS定位、基站定位、WLAN/蓝牙定位（基站定位、WLAN/蓝牙定位后续统称“网络定位技术”）；通过这些定位技术，无论用户设备在室内或是户外，都可以准确地确定设备位置。

Location Kit除了提供基础的定位服务之外，还提供了地理围栏、地理编码、逆地理编码、国家码等功能和接口。

* 坐标：系统以1984年世界大地坐标系统为参考，使用经度、纬度数据描述地球上的一个位置。
* GNSS定位：基于全球导航卫星系统，包含：GPS、GLONASS、北斗、Galileo等，通过导航卫星、设备芯片提供的定位算法，来确定设备准确位置。定位过程具体使用哪些定位系统，取决于用户设备的硬件能力。
* 基站定位：根据设备当前驻网基站和相邻基站的位置，估算设备当前位置。此定位方式的定位结果精度相对较低，并且需要设备可以访问蜂窝网络。
* WLAN、蓝牙定位：根据设备可搜索到的周围WLAN、蓝牙设备位置，估算设备当前位置。此定位方式的定位结果精度依赖设备周围可见的固定WLAN、蓝牙设备的分布，密度较高时，精度也相较于基站定位方式更高，同时也需要设备可以访问网络。


Location Kit在不同的设备上提供不同的能力，并且部分能力依赖于硬件的支持。例如GPS、北斗等定位芯片，可以为设备提供GNSS定位能力；在没有定位芯片但具有WLAN或者蜂窝网络的设备，可以获取WLAN定位、基站定位能力。

## 创建应用



创建一个名为“ArkTSGeoLocationManager”的ArkTS应用作为演示。完整源码见文末。


## 申请权限

 
位置能力作为系统为应用提供的一种基础服务，需要应用在所使用的业务场景，向系统主动发起请求，并在业务场景结束时，主动结束此请求，在此过程中系统会将实时的定位结果上报给应用。

使用设备的位置能力，需要用户进行确认并主动开启位置开关。如果位置开关没有开启，系统不会向任何应用提供定位服务。

设备位置信息属于用户敏感数据，所以即使用户已经开启位置开关，应用在获取设备位置前仍需向用户申请位置访问权限。在用户确认允许后，系统才会向应用提供定位服务。

修改`src/main/module.json5`，补充权限相关内容：

```json
{
  "module": {
    // ...为节约篇幅，此处省略非核心内容

    // 申请位置服务权限
    "requestPermissions": [
      {
        "name": "ohos.permission.LOCATION",
        "reason": "$string:location_permission",
        "usedScene": {
          "abilities": [
            "EntryAbility"
          ],
          "when": "inuse"
        }
      },
      {
        "name": "ohos.permission.APPROXIMATELY_LOCATION",
        "reason": "$string:fuzzy_location_permission",
        "usedScene": {
          "abilities": [
            "EntryAbility"
          ],
          "when": "inuse"
        }
      }
    ]
  }
}
```


修改`src/main/ets/entryability/EntryAbility.ets`，在应用启动时检验权限：

```ts
import { abilityAccessCtrl } from '@kit.AbilityKit';
import { BusinessError } from '@kit.BasicServicesKit';
// ...为节约篇幅，此处省略非核心内容

export default class EntryAbility extends UIAbility {
  onCreate(want: Want, launchParam: AbilityConstant.LaunchParam): void {
    let atManager = abilityAccessCtrl.createAtManager();
    try {
      atManager.requestPermissionsFromUser(this.context,
        ['ohos.permission.LOCATION', 'ohos.permission.APPROXIMATELY_LOCATION'])
        .then((data) => {
          hilog.info(0x0000, 'testTag', `data: ${JSON.stringify(data)}`);
        })
        .catch((err: BusinessError) => {
          hilog.error(0x0000, 'testTag', `err: ${JSON.stringify(err)}`);
        })
    } catch (err) {
      hilog.error(0x0000, 'testTag', `catch err->${JSON.stringify(err)}`);
    }
  }

  // ...为节约篇幅，此处省略非核心内容
}  
```


界面效果如下图所示。

![申请权限](/images/post/20250721-geo-001.png)



## 设计UI



```ts
import { geoLocationManager } from '@kit.LocationKit';

import { BusinessError } from '@kit.BasicServicesKit';

@Entry
@Component
struct Index {
  @State message: string = 'Hello World';

  build() {
    RelativeContainer() {
      Text(this.message)
        .id('HelloWorld')
        .fontSize(30)
        .fontWeight(FontWeight.Bold)
        .alignRules({
          center: { anchor: '__container__', align: VerticalAlign.Center },
          middle: { anchor: '__container__', align: HorizontalAlign.Center }
        })
        .onClick(() => {
          this.getLocation();
        })
    }
    .height('100%')
    .width('100%')
  }

  getLocation() {
    let startTime = new Date().getTime();
    let requestInfo: geoLocationManager.CurrentLocationRequest = {
      'priority': geoLocationManager.LocationRequestPriority.FIRST_FIX,
      'scenario': geoLocationManager.LocationRequestScenario.UNSET, 'maxAccuracy': 100
    };

    let locationChange = (err: BusinessError, location: geoLocationManager.Location): void => {
      if (err) {
        console.error('locationChanger: err=' + JSON.stringify(err));
      }
      if (location) {
        console.log('locationChanger: location=' + JSON.stringify(location));
        this.message =
          '定位信息：' + JSON.stringify(location) + '\n 花费时间：' + (new Date().getTime() - startTime) / 1000
      }
    };
    try {
      geoLocationManager.getCurrentLocation(requestInfo, locationChange);
    } catch (err) {
      console.error("errCode:" + err.code + ",errMessage:" + err.message);
    }
    ;
  }
}
```


当点击界面Text组件时，会触发getLocation()函数的执行。



## 运行调测

当点击界面Text组件时，会触发getLocation()函数的执行，界面效果如下图所示。

![触发getLocation()函数的执行](/images/post/20250721-geo-002.png)




## 参考引用



* 本文同步至：<https://waylau.com/2025-07-21-harmonyos-arkts-geo-location-manager>
* 源码见《跟老卫学仓颉编程语言开发》开源免费教程， <https://github.com/waylau/cangjie-programming-language-tutorial>
* 《跟老卫学AI大模型开发》开源免费教程， https://github.com/waylau/ai-large-model-tutorial/
* 《跟老卫学仓颉编程语言开发》开源免费教程， https://github.com/waylau/cangjie-programming-language-tutorial
* 《鸿蒙HarmonyOS手机应用开发实战》（清华大学出版社）
* 《鸿蒙HarmonyOS应用开发入门》（清华大学出版社）
* “鸿蒙零基础快速实战-仿抖音App开发（ArkTS版）”（https://coding.imooc.com/class/843.html）
* 《鸿蒙HarmonyOS应用开发从入门到精通（第2版）》（北京大学出版社)
* 《鸿蒙之光HarmonyOS NEXT原生应用开发入门》（清华大学出版社)
* “HarmonyOS NEXT+AI大模型打造智能助手APP(仓颉版)”（https://coding.imooc.com/class/927.html）
