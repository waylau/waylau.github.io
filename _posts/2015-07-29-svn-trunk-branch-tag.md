---
layout: post
title: SVN 中的 truck、branch 和 tag
date: 2015-07-29 01:08
author: admin
comments: true
categories: [SVN]
tags: [SVN]
---

## 问题

使用 SVN 进行项目的版本管理时， truck（主干）、branch（分支） 和 tag（标签） 是如何区别？何时使用他们？

<!-- more -->

## branch/tag

版本控制系统的一个特性是能够把各种修改分离出来放在开发品的一个分割线上。这条线被称为 branch。 branch 经常被用来试验新的功能，而不会对开发有编译错误的干扰。当新的功能足够稳定之后，开发品的 branch 就可以 merge（合并）回 主branch(trunk)。

版本控制系统的另一个特性是能够标记特殊的版本(例如某个发布版本)，所以你可以在任何时候重新建立一个特定的构件和环境。这个过程被称作 tag。

Subversion 没有特殊的命令用于 branch 或 tag，而是使用所了所谓的“cheap copies(廉价拷贝）”。廉价拷贝类似于 UNIX 的硬链接，这意味着并不会在存储库中做一个完整的副本，而是创建了一个内部链接，用于指向一个特定的树/修订。这样， branch 或 tag 就能非常快的创建，几乎不会占用没有额外存储库的空间。

### 创建一个branch 或 tag

如果你用推荐的目录结构导入了一个工程，那么创建 branch 或 tag 就非常简单:

![](http://99btgc01.info/uploads/2015/07/001%281%29.png)

在你当前的工作副本中给你你想要复制的 branch 或 tag 选择一个目录，然后选择命令`TortoiseSVN` → `Branch/Tag...`。

默认的目标 URL 将会是你当前工作副本所处的源 URL。你必须给你的branch 或 tag 编辑一个新路径。来取代

    http://svn.collab.net/repos/ProjectName/trunk

你可以使用这样的设置

    http://svn.collab.net/repos/ProjectName/tags/Release_1.10

如果你忘记了你上一次使用的命名约定，可以用鼠标右键打开版本库浏览器来察看已经存在的版本库结构。

现在你必须选择要复制的源位置。在这里你有三个设置选项:

* HEAD revision in the repository:
新 branch 直接从仓库中的 HEAD revision 里复制出来。不需要从你的工作副本中传输任何数据，这个 branch 的建立是非常快的。

* Specific revision in the repository:
在仓库中直接复制建立一个新 branch 同时你也可以选择一个旧版本。假如在你上周发布了项目时忘记了做 tag ，这将非常有用。如果你记不起来版本号，通过点击鼠标右键来显示版本日志，同时从这里选取版本号。和上次一样不需要从你的工作副本中传输任何数据，这个 branch  建立起来是非常快的。

* Working copy:
新的 branch 是一个完全等同于你的本地工作副本的一个副本。如果你更新了一些文件到你的工作副本的某个旧版本里，或者你在本地做出了修改，这些改变将准确无误的进入副本中。自然而然地这种综合的 branch 会包含正在从工作副本传输到版本库的数据，如果这些数据还不存在的话。


## 参考
- <http://tortoisesvn.net/docs/nightly/TortoiseSVN_en/tsvn-dug-branchtag.html>


