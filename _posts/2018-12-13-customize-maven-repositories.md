---
layout: post
title: 自定义Maven的`<repositories>`
date: 2018-12-13 00:22
author: admin
comments: true
categories: [Maven]
tags: [Maven]
---

有时，应用中需要一些比较新的依赖，而这些依赖并没有正式发布，还是处于milestone或者是snapshot阶段，并不能从中央仓库或者镜像站上下载到。此时，就需要
自定义Maven的`<repositories>`。


<!-- more -->

## 自定义Maven的`<repositories>`

以Spring应用程序程序为例，需要添加一个Spring Security 5.2.0.BUILD-SNAPSHOT版本的依赖，可惜这是个snapshot，并不在镜像站中。

解决方法就是在pom.xml添加一个snapshot的地址：

```xml
<!-- Spring Snapshots仓库 -->
<repositories>
    <repository>
        <id>spring-snapshots</id>
        <name>Spring Snapshots</name>
        <url>https://repo.spring.io/snapshot</url>
    </repository>
</repositories>
```


这样，就能从这个仓库中，获取到 Spring Security 5.2.0.BUILD-SNAPSHOT版本的依赖了。

## 检查Maven的settings.xml

但有时，跟着上面的设置方法并不奏效，仍然还是从之前的镜像站中去下载。此时，我们需要去检查下 Maven 安装目录下的settings.xml：


```xml
<mirror>
    <id>nexus-aliyun</id>
    <mirrorOf>*</mirrorOf>
    <name>Nexus aliyun</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>
```

可以看到，在设置镜像时，mirrorOf设置为了“*”，意味所有的依赖都是从这个镜像上下载。完全不给Spring Snapshots机会了。

解决方法也简单，改为下面的方式：


```xml
<mirror>
    <id>nexus-aliyun</id>
    <mirrorOf>*,!spring-snapshots</mirrorOf>
    <name>Nexus aliyun</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>
```


mirrorOf设置为了“*,!spring-snapshots”，意味除了Spring Snapshots外，所有的依赖都是从这个镜像上下载。这样就能下载到 Spring Snapshots了。

以下是mirrorOf的详细解释。


## mirrorOf的配置解释

mirrorOf的配置支持如下场景：

* `*`：所有依赖；
* `external:*`：所有不再本地和不是基于文件的依赖；
* `repo,repo1`：所有来自repo或者repo1仓库的依赖；
* `*,!repo1`：除了repo1外的所有依赖。即上面示例中的场景。


## 参考引用


* 原文同步至<https://waylau.com/2018-12-13-customize-maven-repositories/>