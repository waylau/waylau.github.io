---
layout: post
title: HarmonyOS 3.1/4.0应用升级到HarmonyOS NEXT改动点
date: 2024-07-09 00:22
author: admin
comments: true
categories: [HarmonyOS]
tags: [HarmonyOS, NEXT]
---

在 “2024鸿蒙零基础快速实战-仿抖音App开发（ArkTS版）”（<https://coding.imooc.com/class/843.html>）视频课程中，因为讲师在该课程授课时是使用的HarmonyOS 3.1/4.0应用（API 9），如果部分学员采用了最新的HarmonyOS NEXT API，此时就会遇到API兼容性的问题。

本文整理了从HarmonyOS 3.1/4.0应用升级到HarmonyOS NEXT（Developer Preview2）改动点。


<!-- more -->

![](../images/post/20240709-next-01.png)


### 获取路由参数

```
// 从路由参数中获取用户信息
let userInfo: UserInfo = params[`${Constants.PARAM_LOGGED_USER_INFO_KEY}`];
```

报错：`Indexed access is not supported for fields (arkts-no-props-by-index) <ArkTSCheck>`

不应该通过索引来访问对象


错误原因：在 HarmonyOS Next 开发中，如果你不能使用 TypeScript 或 JavaScript 的标准库，包括 hasOwnProperty 和通过 `[]` 或 `.` 获取对象的属性值，那么你需要寻找其他方法来将对象转换为 Map。由于 ArkTS 对动态特性的限制，你可以考虑使用递归函数来遍历对象的属性，并将它们添加到 Map 中。

改为

```
// 从路由参数中获取用户信息
let userInfo: UserInfo = new Map(Object.entries(params))[`${Constants.PARAM_LOGGED_USER_INFO_KEY}`];
```

```
router.replaceUrl({
  url: 'pages/Index',

  // 通过路由参数，传递待发布的视频数据
  params: {
    [`${Constants.PARAM_PUBLISHED_VIDEO_INFO_KEY}`]: this.selectedVideoInfo
  }
})
```

报错：`Objects with property names that are not identifiers are not supported (arkts-identifiers-as-prop-names) <ArkTSCheck>`



改为

```
router.replaceUrl({
  url: 'pages/Index',

  // 通过路由参数，传递待发布的视频数据
  params: {
    'publishedVideoInfo': this.selectedVideoInfo
  }
})
```

### 设置应用全局的UI状态存储

```
AppStorage.SetOrCreate('UserInfo.username', userInfo.username);
```

改为

```
AppStorage.setOrCreate('UserInfo.username', userInfo.username);
```


```
let username: string = AppStorage.Get('UserInfo.username');
```

改为

```
let username: string = AppStorage.get('UserInfo.username') as string;
```


### 不要导出`@Entry`




```
@Entry
@Component
export struct VideoPublishPage {
```



```
@Entry
@Component
struct VideoPublishPage {
```


### Property 'videoId' has no initializer and is not definitely assigned in the constructor.


```
export class VideoInfo {
  // 视频ID
  videoId: number;
```

```
 Property 'videoId' has no initializer and is not definitely assigned in the constructor.
```

改为

```
export class VideoInfo {
  // 视频ID
  videoId: number = 0;
```


## 参考资料


* 本文同步至：<https://waylau.com/changes-upgrade-from-3-1-4-0-to-harmonyos-next-developer-preview2/>
* 《跟老卫学HarmonyOS开发》 开源免费教程，<https://github.com/waylau/harmonyos-tutorial>
* 《鸿蒙HarmonyOS手机应用开发实战》（清华大学出版社）
* 《鸿蒙HarmonyOS应用开发从入门到精通战》（北京大学出版社）
* “鸿蒙系统实战短视频App 从0到1掌握HarmonyOS”（<https://coding.imooc.com/class/674.html>）
* 《鸿蒙HarmonyOS应用开发入门》（清华大学出版社）
* “2024鸿蒙零基础快速实战-仿抖音App开发（ArkTS版）”（<https://coding.imooc.com/class/843.html>）