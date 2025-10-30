---
layout: post
title: HarmonyOS Navigation嵌套Tabs导致Tabs布局异常问题解决
date: 2025-10-30 00:22
author: admin
comments: true
categories: [HarmonyOS]
tags: [HarmonyOS]
---

本文介绍了在HarmonyOS应用开发过程中，遇到了Navigation嵌套Tabs导致Tabs布局异常的问题，查明了问题原因并给出了解决方案。

<!-- more -->


## 问题背景

在某个应用里面，需要使用HarmonyOS的Navigation和Tabs，其中，Navigation嵌套了Tabs，代码如下：

```ts
build() {
  Navigation(this.pageInfos) {

    Tabs({ barPosition: BarPosition.End }) {
      TabContent() {
      }
      .tabBar(this.tabBuilder('首页', 0, $r('app.media.home'), $r('app.media.home')))

      TabContent() {
      }
      .tabBar(this.tabBuilder('拍照', 1, $r('app.media.camera'), $r('app.media.camera')))
    }
    .width('100%')
    .height('100%')

  }.mode(NavigationMode.Stack)
  .width('100%')
  .height('100%')

}
```

但可以看到 Tabs 具体底部有非常大的一段空间。如下图所示。

![](/images/post/20251030-harmonyos6-001.png)

如果将Navigation去除，Tabs正常显示。如下图所示。


![](/images/post/20251030-harmonyos6-002.png)

## 问题原因


Tabs跟Navigation底部工具栏冲突。因为Navigation并未配置toolbarConfiguration，因此并未底部工具栏，但空间还是被底部工具栏占据了。

## 解决方案

### 方案1

将Navigation底部工具栏禁用就好。代码如下：



```ts
build() {
  Navigation(this.pageInfos) {

    Tabs({ barPosition: BarPosition.End }) {
      TabContent() {
      }
      .tabBar(this.tabBuilder('首页', 0, $r('app.media.home'), $r('app.media.home')))

      TabContent() {
      }
      .tabBar(this.tabBuilder('拍照', 1, $r('app.media.camera'), $r('app.media.camera')))
    }
    .width('100%')
    .height('100%')

  }.mode(NavigationMode.Stack)
  .width('100%')
  .height('100%')
  // 禁用底部工具栏
  .hideToolBar(true)

}
```



### 方案2

更好的方案是直接使用Navigation底部工具栏来替换掉Tabs，本质上Tabs和Navigation底部工具栏功能上有重叠。示例代码如下：



```ts
build() {
  Navigation(this.pageInfos) {
 

  }.mode(NavigationMode.Stack)
  .width('100%')
  .height('100%')
  // 禁用底部工具栏
  // .hideToolBar(true)
  .toolbarConfiguration([
    {
      value: '首页',
      icon: $r("app.media.home")
    },
    {
      value: '拍照',
      icon: $r("app.media.camera")
    }
  ])
}
```


效果如下：

![](/images/post/20251030-harmonyos6-003.png)

## 更多参考

加入鸿蒙生态，共建万物互联。以下是鸿蒙应用开发常用教程。



* 本文同步至：<https://waylau.com/nested-tabs-in-harmonyos-navigation/>
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