---
layout: post
title: Git导出某个分支的干净文件
date: 2017-08-06 00:22
author: admin
comments: true
categories: [Git]
tags: [Git]
---


经常需要将某个分支的文件打包发送，而且是干净的（不带版本控制信息），下面的语句很重要哦：


```
>git archive --format zip --output "./2.0.0-20170806.zip" 2.0.0-20170806
```

<!-- more -->

就能在当前目录下，将  `2.0.0-20170806` 分支的内容，导出为 `2.0.0-20170806.zip` 压缩文件。

