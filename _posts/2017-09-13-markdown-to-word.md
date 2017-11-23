---
layout: post
title: Markdown 转为 Word 格式文档
date: 2017-09-13 00:22
author: admin
comments: true
categories: [Markdown,Pandoc,Writage]
tags: [Markdown,Pandoc,Writage,Word]
---

平时都有用 Markdown 写书、写博客的习惯。很享受 Markdown 带来的纯粹撰写文字，而无须纠结格式所带来的快感。但有时，某些场合下，Word 文件又是必须的格式（比如，编辑部校稿，公司会议等），此时，就很有必要将Markdown 转为 Word 格式文档。

本文演示了几种方面将 Markdown 转为 Word 格式文档的方法。

<!-- more -->

## 工具

* Pandoc <http://www.pandoc.org/>
* Writage <http://www.writage.com/>  

### Pandoc

Pandoc 是一种方面将各种文档格式互转的方面的工具。当然，他是再命令行执行的，所以对用户的动手能力有一点点要求。

用法：

```
pandoc -f markdown -t docx -o 内容简介.docx  docs/Introduction.md
``` 

其中， `-f` 为文档来源格式，`-t` 指定了目标的文档格式，`-o` 为输出的文档名称， `docs/Introduction.md` 为待转换的文件。


### Writage

Writage 是一个 Word 插件，只要安装之后，就可以在 Word 中方面的进行文件的相互转换。

## 如何批量转换 Gitbook 格式文档

习惯用 Gitbook 格式来写书。其实，Gitbook 中的 SUMMARY.md 文件就是所有文档的目录，稍作修改，就能形成 Pandoc 所能执行的脚本。这样就能快速进行批量转换。用法如下：

* `* [` 替换为 `pandoc -f markdown -t docx -o `
* `]( ` 替换为  `.docx  `
* `)` 替换为空格

最终会形成如下的脚本：

```
pandoc -f markdown -t docx -o 内容简介.docx  docs/Introduction.md
pandoc -f markdown -t docx -o 前言.docx  docs/Preface.md
```

重新保存为.bat 文件，双击运行即可。
