---
layout: post
title: Gradle Wrapper 引用本地的发布包
date: 2017-06-04 00:22
author: admin
comments: true
categories: [Gradle]
tags: [Gradle,Gradle Wrapper]
---

Gradle Wrapper 免去了用户在使用 Gradle 进行项目构建时需要安装 Gradle 的繁琐步骤。 每个 Gradle Wrapper 都绑定到一个特定版本的 Gradle，所以当你第一次在给定 Gradle 版本下运行上面的命令之一时，它将下载相应的 Gradle 发布包，并使用它来执行构建。默认，Gradle Wrapper 的发布包是指向的官网的 Web 服务地址，有时候，下载这个发布包比较慢甚至不成功，本文演示了加速下载发布包的方式。 

<!-- more -->

## Gradle Wrapper 的配置


Gradle Wrapper 的配置在`gradle/wrapper/gradle-wrapper.properties`，其默认的配置如下：

```shell
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-3.5-bin.zip
```

其中，`distributionUrl` 指明了 Gradle Wrapper 下载 Gradle 发布包的位置。如果遇到下载这个发布包比较慢甚至不成功的时候，可以将该地址引到本地的文件，比如：

```
...
#distributionUrl=https\://services.gradle.org/distributions/gradle-3.5-bin.zip
distributionUrl=file\:/D:/software/webdev/java/gradle-3.5-all.zip
...
```

这样构建的速度将会非常快了。当然，前提是，要实现准本好发布包放到本地。 


 
## 参考文献

* 《Gradle 3 用户指南》：<https://github.com/waylau/gradle-3-user-guide>



