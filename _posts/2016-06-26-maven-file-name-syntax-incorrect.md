---
layout: post
title: Maven 项目构建失败“文件名、目录名或卷标语法不正确”
date: 2016-06-30 01:41
author: admin
comments: true
categories: [Maven]
tags: [Maven]
---

在修改国家项目中间件 edt 时，用 Maven 编译 necc-edt-api 模块时，总是编译不过，并提示了“文件名、目录名或卷标语法不正确”的错误。

<!-- more -->

## 错误提示

观察控制台，输出如下：
 
```
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 32.730 s
[INFO] Finished at: 2016-06-26T18:59:50+08:00
[INFO] Final Memory: 42M/336M
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-jar-plugin:2.4:jar
 (default-jar) on project necc-edt-api: Error assembling JAR: Problem creating j
ar: D:\workspaceZYH\necc_20150817\edt\api\target\necc-edt-api-2016-06-26T10:59:1
8Z.jar (文件名、目录名或卷标语法不正确。) (and the archive is probably corrupt b
ut I could not delete it) -> [Help 1]
[ERROR]
```

中文提示很友好且明确“文件名、目录名或卷标语法不正确”，
再观察生成的jar 名称，居然是 
“necc-edt-api-2016-06-26T10:59:1 8Z.jar”，显然“：”（冒号） 是不能作为文件名称的。

## 解决

查看该模块 pom.xml 中文件名生成的策略：
 
```xml
 <build>
 ....
        <finalName>${project.artifactId}-${maven.build.timestamp}</finalName>
 ....
 </build>
```

命名使用了 Maven 内置的属性 `${maven.build.timestamp}` ， 该属性是指项目构件开始时间。显然时间中时分秒是带有了“：”（冒号）的。
 
解决方式：自然是去掉时间中的“：”
 
对上述时间进行格式化，使用了另外一个 Maven 内置的属性来格式话时间，如下：

pom.xml 添加如下：
 
```
<properties>
...
    <maven.build.timestamp.format>yyyy-MM-dd</maven.build.timestamp.format>
...
</properties>
```

再次构建,成功

```
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary:
[INFO]
[INFO] NECC Parent ........................................ SUCCESS [  0.885 s]
[INFO] NECC Database Table Access Component. .............. SUCCESS [  8.129 s]
[INFO] NECC Bussiness Model Component. .................... SUCCESS [  2.722 s]
[INFO] necc-edt ........................................... SUCCESS [  0.043 s]
[INFO] necc-edt-protocol .................................. SUCCESS [  1.122 s]
[INFO] NECC Core Service JAVA API Component. .............. SUCCESS [  1.605 s]
[INFO] NECC Common Component. ............................. SUCCESS [  1.600 s]
[INFO] NECC Task Runner Component. ........................ SUCCESS [  0.410 s]
[INFO] NECC Core Service JAVA IMPL Component. ............. SUCCESS [  4.209 s]
[INFO] NECC Service Facade Component. ..................... SUCCESS [  1.782 s]
[INFO] NECC Core EDT Kernel Component. .................... SUCCESS [  3.537 s]
[INFO] necc-edt-api ....................................... SUCCESS [  8.250 s]
[INFO] necc-edt-client .................................... SUCCESS [  6.947 s]
[INFO] NECC Core EDT register Component. .................. SUCCESS [  4.870 s]
[INFO] necc-edt-pump ...................................... SUCCESS [ 15.917 s]
[INFO] NECC Core EDT sync Component. ...................... SUCCESS [  3.257 s]
[INFO] NECC Task Repository Component. .................... SUCCESS [  0.456 s]
[INFO] NECC Web Government Component. ..................... SUCCESS [ 44.963 s]
[INFO] NECC Web Enterprise Component. ..................... SUCCESS [ 19.090 s]
[INFO] NECC Task Administrator Component. ................. SUCCESS [  2.860 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:13 min
[INFO] Finished at: 2016-06-26T19:12:38+08:00
[INFO] Final Memory: 60M/717M
[INFO] ------------------------------------------------------------------------
```
 
生成文件格式为：necc-edt-api-2016-06-26.jar


## 相关知识

Maven共有6类属性,内置属性 Maven 预定义,用户可以直接使用:

* `${basedir}`表示项目根目录,即包含 pom.xml 文件的目录;
* `${version}`表示项目版本;
* `${project.basedir}`同`${basedir}`;
* `${project.baseUri}`表示项目文件地址;
* `${maven.build.timestamp}`表示项目构件开始时间;
* `${maven.build.timestamp.format}`表示属性`${maven.build.timestamp}`的展示格式,默认值为 yyyyMMdd-HHmm,可自定义其格式,其类型可参考