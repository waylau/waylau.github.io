---
layout: post
title: HarmonyOS Navigation实现导航与路由切换
date: 2025-08-31 00:22
author: admin
comments: true
categories: [HarmonyOS]
tags: [HarmonyOS, ArkTS， Navigation]
---

本文主要介绍在HarmonyOS下如何使用Navigation和NavDestination来实现导航与路由切换。

<!-- more -->

## Navigation和NavDestination概述

Navigation组件是路由导航的根视图容器，一般作为Page页面的根容器使用，其内部默认包含了标题栏、内容区和工具栏，其中内容区默认首页显示导航内容（Navigation的子组件）或非首页显示（NavDestination的子组件），首页和非首页通过路由进行切换。

从API Version 10开始，推荐使用NavPathStack配合navDestination属性进行页面路由。

## 创建应用



创建一个名为“ArkTSNavigation”的ArkTS应用作为演示。完整源码见文末。


## 首页

 

修改`src/main/ets/pages/Index.ets`，内容如下：

```ts
@Entry
@Component
struct Index {
  @State message: string = '主页';

  // 创建一个导航控制器对象并传入Navigation
  pageInfos: NavPathStack = new NavPathStack();

  build() {
    Navigation(this.pageInfos) {
      Column() {
        Text(this.message)
          .fontSize($r('app.float.page_text_font_size'))
          .fontWeight(FontWeight.Bold)
          .alignRules({
            center: { anchor: '__container__', align: VerticalAlign.Center },
            middle: { anchor: '__container__', align: HorizontalAlign.Center }
          })

        Button('导航到第一页')
          .width(128).height(28)
          .onClick(() => {
            // 跳转页面时不携带参数
            this.pageInfos.pushPathByName('pageOne', null);
          })

        Button('导航到第二页')
          .width(128).height(28)
          .onClick(() => {
            // 跳转页面时携带参数
            this.pageInfos.pushPathByName('pageTwo', "Page Two");
          })
      }
      .height('100%')
      .width('100%')
    }
    .mode(NavigationMode.Auto)
  }
}
```


上述页面

* 通过NavPathStack来初始化Navigation。NavPathStack是Navigation路由栈，允许被继承。开发者可以在派生类中新增属性方法，也可以重写基类NavPathStack的方法。派生类对象可以替代基类NavPathStack对象使用。
* 设置了两个按钮，点击按钮会跳转到不同的页面。通过pushPathByName等路由接口进行页面跳转。其中一个按钮会跳转到一个新页面，不并携带参数；另一个按钮会跳转到一个新页面，并携带参数内容为“Page Two”。





## 第一个子页面

新建`src/main/ets/pages/PageOne.ets`：

```ts
@Builder
export function PageOneBuilder(name: string, param: Object) {
  PageOne()
}

@Component
export struct PageOne {
  @State message: string = '第一页';
  pageInfos: NavPathStack = new NavPathStack();

  build() {
    NavDestination() {
      RelativeContainer() {
        Text(this.message)
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
    .title('PageOne')
    .onReady((context: NavDestinationContext) => {
      this.pageInfos = context.pathStack;
    })
  }
}
```

通过`@Builder`定义的函数PageOneBuilder来构建PageOne。函数名称需要和route_map.json配置文件中的buildFunction保持一致，否则在编译时会报错。

NavDestination作为子页面的根容器，用于显示Navigation的内容区。

当切换到该界面时，会显示页面内容“第一页”。



## 第二个子页面

新建`src/main/ets/pages/PageTwo.ets`：

```ts
@Builder
export function PageTwoBuilder(name: string, param: Object) {
  PageTwo()
}

@Component
export struct PageTwo {
  @State message: string = '第二页';
  pageInfos: NavPathStack = new NavPathStack();

  build() {
    NavDestination() {
      RelativeContainer() {
        Text(this.message)
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
    .title('PageTwo')
    .onReady((context: NavDestinationContext) => {
      this.pageInfos = context.pathStack
      this.message = context.pathInfo.param as string;
    })
  }
}
```

大体与PageOne类似，确保在于，会通过NavDestinationContext来接收从首页路由过来传递的参数。

当切换到该界面时，会显示从首页传递的参数“Page Two”。


## 设置路由

Navigation支持使用系统路由表的方式进行动态路由。各业务模块（HSP/HAR）中需要独立配置route_map.json文件，在触发路由跳转时，应用只需要通过NavPathStack提供的路由方法，传入需要路由的页面配置名称，此时系统会自动完成路由模块的动态加载、页面组件构建，并完成路由跳转，从而实现了开发层面的模块解耦。系统路由表不支持预览器，跨平台及模拟器。其主要步骤如下。



修改`src/main/module.json5`添加路由表配置：



```json5
{
  "module" : {

    //...为节约篇幅，此处省略非核心内容

    "routerMap": "$profile:route_map"
  }
}
```

添加完路由配置文件地址后，需要在工程`resources/base/profile`中创建route_map.json文件。添加如下配置信息：


```json
{
  "routerMap": [
    {
      "name": "pageOne",
      "pageSourceFile": "src/main/ets/pages/PageOne.ets",
      "buildFunction": "PageOneBuilder",
      "data": {
        "description": "this is pageOne"
      }
    },
    {
      "name": "pageTwo",
      "pageSourceFile": "src/main/ets/pages/PageTwo.ets",
      "buildFunction": "PageTwoBuilder",
      "data": {
        "description": "this is pageTwo"
      }
    }
  ]
}
```

上述配置


* name	跳转页面名称。
* pageSourceFile	跳转目标页在包内的路径，相对src目录的相对路径。
* buildFunction	跳转目标页的入口函数名称，必须以@Builder修饰。
* data	应用自定义字段。可以通过配置项读取接口getConfigInRouteMap获取。


在跳转目标页面中，需要配置入口Builder函数，函数名称需要和route_map.json配置文件中的buildFunction保持一致，否则在编译时会报错。

## 运行调测

界面效果如下图所示。

![运行调测](/images/post/20250831-arktnavigation.gif)




## 参考引用



* 本文同步至：<https://waylau.com/2025-07-21-harmonyos-arkts-geo-location-manager>
* 源码见《跟老卫学HarmonyOS开发》开源免费教程， <https://github.com/waylau/harmonyos-tutorial>
* 《跟老卫学AI大模型开发》开源免费教程， https://github.com/waylau/ai-large-model-tutorial/
* 《跟老卫学仓颉编程语言开发》开源免费教程， https://github.com/waylau/cangjie-programming-language-tutorial
* 《鸿蒙HarmonyOS手机应用开发实战》（清华大学出版社）
* 《鸿蒙HarmonyOS应用开发入门》（清华大学出版社）
* “鸿蒙零基础快速实战-仿抖音App开发（ArkTS版）”（https://coding.imooc.com/class/843.html）
* 《鸿蒙HarmonyOS应用开发从入门到精通（第2版）》（北京大学出版社)
* 《鸿蒙之光HarmonyOS NEXT原生应用开发入门》（清华大学出版社)
* “HarmonyOS NEXT+AI大模型打造智能助手APP(仓颉版)”（https://coding.imooc.com/class/927.html）
