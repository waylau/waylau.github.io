---
layout: post
title:   Apache Maven 3.1.0 安装、部署、使用
date:   2013-09-03 02:34
author: admin
comments: true
categories: [Java,Maven]
tags: [Java,Maven]
---

##一、下载

下载页面<http://maven.apache.org/download.cgi> 找到最新的 ，本例为Maven 3.1.0

<http://mirrors.cnnic.cn/apache/maven/maven-3/3.1.0/binaries/apache-maven-3.1.0-bin.zip>

##二、安装

1.解压zip ,将apache-maven-3.1.0文件夹拷贝至C:\Program Files\Apache Software Foundation 目录下

2.在环境变量中添加一个用户变量（如果不成功，也可以添加到系统变量中）

变量：`M2_HOME`

值：`C:\Program Files\Apache Software Foundation\apache-maven-3.1.0`

3.在环境变量中的系统变量的path添加一个

`%M2_HOME%\bin`

4.校验

Cmd 模式下 输入

	mvn --version

出现以下画面，则安装成功！
<img src="http://b.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=4425cc3fd0c8a786ba2a4a0b5732b800/94cad1c8a786c9171e043efcc83d70cf3ac757eb.jpg?referer=ca22ceded50735fac8e77a8991a9&x=.jpg"/>

##三、配置

建一个文件夹作为仓位：
我的是在`d:\ workspaceMaven`

`C:\Program Files\Apache Software Foundation\apache-maven-3.1.0\conf`目录下找到
settings.xml文件，

打开文件找到被注释的 `<localRepository>/path/to/local/repo</localRepository>`

将它启动，写上仓库的路径，

即为`<localRepository>D:\workspaceMaven</localRepository>`

如下效果

<img src="http://b.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=e48cddc0e4dde711e3d243f397d4bf26/8435e5dde71190ef4728ff1acf1b9d16fcfa6027.jpg?referer=80db6b0f838ba61e86f9ff1fc0fd&x=.jpg"/>

打开cmd，输入 

	mvn help:system

如果配置正确，将开始从中央仓库下载文件至4中配置的本地仓库。该过程持续时间因网络带宽影响可能持续时间很长。

见下图，则配置成功
<img src="http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=aeb08dca8694a4c20e23e72e3ecf6ae8/d53f8794a4c27d1eaa2357011ad5ad6edcc438e9.jpg?referer=00c8aec3aec37931247fb21978a8&x=.jpg"/>

仓库下会多出很多文件

<img src="http://e.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=386fd9ff18d8bc3ec20806cfb2b0d723/810a19d8bc3eb1356714db7ea71ea8d3fc1f4427.jpg?referer=29f08bee0ff41bd58344dfc4a4fd&x=.jpg">

##四、使用

找一个maven的项目。本例为“cat78”，放到任意目录下
<img src="http://f.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=b6743b25f603918fd3d13dcf610657aa/63d0f703918fa0ecdda908c8279759ee3c6ddb8d.jpg?referer=fd676d6e023b5bb5e7c014ce2583&x=.jpg">

2.cmd 下 执行cd cat78到该目录下

运行

	mvn clean compile

此时，项目开始编译

又需要一段时间来下载相关jar包、pom文件

3.编译完成 ，会生产.bat可运行文件，双击运行就行了~

完整的使用教程，请访问[使用maven编译Java项目](http://www.waylau.com/build-java-project-with-maven/)