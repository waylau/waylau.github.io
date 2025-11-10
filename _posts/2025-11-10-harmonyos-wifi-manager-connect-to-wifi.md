---
layout: post
title: HarmonyOS通过wifiManager来连接Wi-Fi
date: 2025-11-05 00:22
author: admin
comments: true
categories: [HarmonyOS]
tags: [HarmonyOS]
---

本文介绍了在HarmonyOS应用开发过程中，如何通过wifiManager来连接Wi-Fi。
<!-- more -->


## Wi-Fi及STA模式简介


无线局域网（Wireless Local Area Networks，WLAN），是通过无线电、红外光信号或者其他技术发送和接收数据的局域网，用户可以通过WLAN实现节点之间无物理连接的网络通讯。常用于用户携带可移动终端的办公、公众环境中。

Wi-Fi（Wireless Fidelity），是一种基于无线电波的通信技术，用于设备之间传输数据，使得用户可以无需使用有线连接即可访问互联网和共享数据。

WLAN系统为用户提供接入WLAN网络功能（STA模式）、点对点的数据传输功能（P2P模式）和热点分享功能（AP模式），让应用可以通过WLAN和其他设备互联互通。

Wi-Fi STA模式（Station Mode，站点模式）是无线设备作为客户端接入无线局域网（WLAN）的工作模式。在该模式下，设备（如手机、电脑、平板等）通过连接到接入点（AP，Access Point）或无线路由器，实现对网络的访问。


## 应用实现


应用实现步如下：

1. 建立Wi-Fi连接
2. 导入需要的Wi-Fi模块。
3. 开启设备的Wi-Fi。
4. 需要SystemCapability.Communication.WiFi.STA系统能力。
5. 需要申请权限`ohos.permission.GET_WIFI_INFO`，`ohos.permission.SET_WIFI_INFO`。

示例代码：


```ts
import { wifiManager }  from '@kit.ConnectivityKit';

@Entry
@Component
struct Index {
  @State ssid: string = ''
  @State securityType: string = ''
  @State password: string = ''


  build() {
    Column() {

      Row() {
        Text('WiFi名称：')
        TextInput({ text: this.ssid })
          .width('80%')
          .onChange((value) => {
            this.ssid = value
          })
      }.margin(10)

      Row() {
        Text('密码：')
        TextInput({ text: this.password })
          .type(InputType.Password)
          .width('80%')
          .onChange((value) => {
            this.password = value
          })
      }.margin(10)

      // 操作按钮
      Row() {
        Button('连接该网络')
          .type(ButtonType.Capsule)
          .fontColor(Color.White)
          .margin(10)
          .onClick(() => {
            // 连接到WiFi
            this.connectToWifi()
          })

      }
      .width('100%')
      .height('10%')
      .justifyContent(FlexAlign.Center)
    }
    .height('100%')
    .width('100%')
    .justifyContent(FlexAlign.Center)
  }

  // 连接到WiFi
  private connectToWifi() {

    let config:wifiManager.WifiDeviceConfig = {
      ssid : this.ssid,
      preSharedKey : this.password,
      securityType : 3
    }

    console.info('config:' + JSON.stringify(config));

    // 添加候选网络配置
    wifiManager.addCandidateConfig(config).then(result => {
      // 连接至网络
      console.info('addCandidateConfig result:' + JSON.stringify(result));

      wifiManager.connectToCandidateConfig(result);
    }).catch((err: number) => {
      console.error('failed:' + JSON.stringify(err));
    });

  }
}
```


权限设置如下：


```json
"requestPermissions": [
  {
    "name": "ohos.permission.SET_WIFI_INFO",
    "usedScene": {
      "abilities": [
        "EntryAbility"
      ],
      "when": "inuse"
    }
  },
  {
    "name": "ohos.permission.GET_WIFI_INFO",
    "usedScene": {
      "abilities": [
        "EntryAbility"
      ],
      "when": "inuse"
    }
  }
]
```


运行应用，界面效果如下。



![](/images/post/20251110-harmonyos6-001.jpg)



输入Wi-Fi账号、密码，点击应用“连接该网络”，界面效果如下。


![](/images/post/20251110-harmonyos6-002.jpg)

![](/images/post/20251110-harmonyos6-003.jpg)

在浏览器里面访问被访问的应用，界面效果如下。

![](/images/post/20251110-harmonyos6-004.jpg)




完整示例详见文末链接《跟老卫学HarmonyOS开发》中“ArkTSWifiManagerConnectToWifi”。


## 参考引用

加入鸿蒙生态，共建万物互联。以下是鸿蒙应用开发常用教程。



* 本文同步至：<https://waylau.com/harmonyos-wifi-manager-connect-to-wifi/>
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