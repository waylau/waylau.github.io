---
layout: post
title: Markdown 写作常用语法格式
date: 2018-07-07 00:22
author: admin
comments: true
categories: [Markdown]
tags: [Markdown]
---

有网友问笔者，老师，为啥您写作速度这么快，有啥诀窍没有？其实，除了知识积累之外，笔者平常习惯用 Markdown 格式的文档来写东西（<https://waylau.com/books/>）。无论是项目文档，还是博客、文章，使用 Markdown 都能轻松驾驭文档格式，提升写作的速度。

本文整理了笔者经常使用的 Markdown 语法格式。

<!-- more -->


## 标题

写书时，目录一般会细化到三级目录，如果内容较多，还可以细化到四级标题。每章正文一般是从第二级标题开始写。示例如下：


```
# 11.5 二级标题

正文...

## 11.5.1 三级标题

正文...


### 四级标题

正文...
```

## 代码

代码注明具体的语言。

## 图例

正文中如下有插图，格式如下：

```
![图10-15 Actuator 端点](../../images/ch10/10-15-actuator-heath.jpg)

图10-15 Actuator 端点
```


## 注脚


```
3年后的2020年，Adobe 将正式停止支持Flash，Flash终将会走到生命的终点<sup>[1]</sup>。


<sub>[1]该报道可见BBC新闻“Adobe to kill off Flash plug-in by 2020”（<http://www.bbc.com/news/technology-40716304>）</sub>
```

## 表格

```
范围 | 描述
--- | ---
singleton | 每个Spring容器有一个实例（默认值）
prototype | 允许bean可以被多次实例化（使用一次就创建一个实例）
request | 定义bean的scope是HTTP请求。每个HTTP请求都有自己的实例。只有在使用有Web功能的Spring上下文时才有效
```

显示效果如下：


范围 | 描述
--- | ---
singleton | 每个Spring容器有一个实例（默认值）
prototype | 允许bean可以被多次实例化（使用一次就创建一个实例）



## 格式转换

如果是出版写作，往往交给出版社的是 Word 格式的文档，此时需要将 Markdown 文件进行转换。笔者一般使用 Pandoc，使用方法可以参见《[Markdown 转为 Word 格式文档](https://waylau.com/markdown-to-word/)》。