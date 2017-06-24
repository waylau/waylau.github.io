---
layout: post
title: 使用 Webix 创建 Email 客户端
date: 2017-06-24 00:22
author: admin
comments: true
categories: [Webix]
tags: [Webix]
---

Webix 是一个JavaScript UI 库，提供了多达88个UI小部件和功能丰富的 CSS/HTML5 JavaScript 控件。使用 Webix 可以有效地加快 Web 应用的开发。文本将演示了如何通过 Webix 框架，来创建一个 Email 客户端原型。

<!-- more -->

## 安装 Webix

可以下载 Webix 的 JS、CSS 文件，但最快的方式是使用 Webix 的 CDN， 如下：

```html
<!DOCTYPE HTML>
<html>
    <head>
    <link rel="stylesheet" href="http://cdn.webix.com/edge/webix.css" type="text/css"> 
    <script src="http://cdn.webix.com/edge/webix.js" type="text/javascript"></script>  
    </head>
    ...
</html>
```

## 快速开始

我们为我们的第一个应用创建第一个页面 index.html。在 `<body>` 中定义 js 脚本，来放置 UI 配置：

```html
<!DOCTYPE HTML>
<html>
    <head>
    <link rel="stylesheet" href="http://cdn.webix.com/edge/webix.css" type="text/css"> 
    <script src="http://cdn.webix.com/edge/webix.js" type="text/javascript"></script>  
    </head>
    <body>
        <script type="text/javascript" charset="utf-8">
        /*  UI 配置 */
        </script>
    </body>
</html>
```


接着，我们编写 UI 配置：

```js
webix.ui({
    rows: [
        {
            view: "template",
            type: "header", 
            template: "我的应用!"
        },
        {
            view: "datatable",
            autoConfig: true,
            data: [
                { title: "Way Lau", year: 1987, votes: 533848, rating: 8.9, rank: 5 },
                { title: "老卫", year: 1987, votes: 53248, rating: 5.9, rank: 6 }
            ]
        }
    ]
});
```

为了能更加友好显示中文，我们在`<head>`标签里面加上`<meta charset="UTF-8">`。

这样，我们完整的第一个应用的代码如下：

```html
<!DOCTYPE HTML>
<html>

<head>
    <meta charset="UTF-8">
    <link rel="stylesheet" href="http://cdn.webix.com/edge/webix.css" type="text/css">
    <script src="http://cdn.webix.com/edge/webix.js" type="text/javascript"></script>
</head>

<body>
    <script type="text/javascript" charset="utf-8">
        /*  UI 配置 */
        webix.ui({
            rows: [
                {
                    view: "template",
                    type: "header", 
                    template: "我的应用!"
                },
                {
                    view: "datatable",
                    autoConfig: true,
                    data: [
                        { title: "Way Lau", year: 1987, votes: 533848, rating: 8.9, rank: 5 },
                        { title: "老卫", year: 1987, votes: 53248, rating: 5.9, rank: 6 }
                    ]
                }
            ]
        });
    </script>
</body>

</html>
```


用浏览器直接打开我们的index.html 页面，可以看到如下效果：

![](../images/post/20170624-webix-quick-start.jpg)

## 探索项目


那么，我们来简单介绍下 Webix 的原理。

Webix 的应用程序都是放置在 script  脚本中：


```js
webix.ui({
  // 组件
});
```

需要注意的是，如果想让 Webix 脚本在 HTML 文档加载完了再执行，可以使用 `webix.ready(function(){ ....})` 来包裹我们的 Webix，用法如下：


```js
webix.ready(function(){
  webix.ui({
      ....
  });
});
```

下面代码是用 Webix 中的 `view` 来定义一个视图组件，多个 `view` 可以实现复杂的应用布局结构：

```js

rows: [
    {
        view: "template",
        type: "header", 
        template: "我的应用!"
    },
    {
        view: "datatable",
        autoConfig: true,
        data: [
            { title: "Way Lau", year: 1987, votes: 533848, rating: 8.9, rank: 5 },
            { title: "老卫", year: 1987, votes: 53248, rating: 5.9, rank: 6 }
        ]
    }
]
```

在上述例子中，我们用到了两种类型的 `view`，其中，

* `rows` 代码垂直布局的多个列，这个，我们每个行（row）就是一个`view`；
* [ui.template](https://docs.webix.com/api__refs__ui.template.html) 是一个用于包裹 HTML 内容的容器。这里我们用来类型为`header`的`template`来说明这个是应用头。更多 `template` 的类型，可以自行参阅<https://docs.webix.com/samples/80_docs/template_types.html>；
* [ui.datatable](https://docs.webix.com/api__refs__ui.datatable.html) 是一个功能丰富的数据表格组件；
    * autoConfig 设置为 true，表明表格会根据数据来自适应；
    * data 就是表格中放置的数据

## 进阶

在快速了解 Webix 的相关概念之后，我们就要来创建一个稍微复杂一点的应用，就是本文的主要内容“Email 客户端”。


### 布局

从大布局开始，再逐步求精，是构建前端应用的基本思路。我们创建了如下布局结构：


![](../images/post/20170624-webix-layout.jpg)


```js
webix.ui({
    type: "space",
    rows: [
        /* 1st row. Toolbar */
        {
            template: "Toolbar",
            height: 45,
            
        },
        /* 2nd row. The rest of application */
        {
            type: "wide", 
            cols: [
                /* 1st column of the second row.
                /* Folder tree and Calendar */
                {
                    type: "clean",
                    rows: [
                        {
                            template: "Tree",
                            width: 280
                        },
                        {
                            template: "Calendar"
                        }
                    ]
                },
                /* 2nd column of the second row.
                /* Email list, Buttons, and Message reader */
                {
                    type: "wide",                    
                    rows: [
                        { template: "Email List" },
                        {
                            height: 45, cols: [
                                { template: "Button1" },
                                { template: "Button2" },
                                {},
                                { template: "Button 3" }
                            ]
                        },
                        { template: "Message" }
                    ]
                }
            ]
        }
    ]
});
```

其中：

* cols 就是列，每行（row）可能包含了多个列（col）；
* height 和 width 属性来定义视图所需的大小了
* type，它定义了布局边框。如果使用`clean`将获得无边框的单元格，使用`wide`将获得有边框的、有更大空间的单元格。


### 实现 Toolbar

Toolbar（工具栏）可以包含各种元素，如按钮或下拉菜单等。

记住，要使用Webix创建组件，必须使用view:“component_name”代码行，元素属性允许选择工具栏的内容。



* elements 用来放置子的`view`组件。

这里，我们使用了[ui.chart](https://docs.webix.com/api__refs__ui.chart.html)，来创建图表
## 源码

* 

## 参考文献

* <https://opensource.com/article/17/5/10-step-guide-webix-framework>
* <https://docs.webix.com/tutorials__quick_start.html>


