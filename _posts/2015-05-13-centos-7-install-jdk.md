---
layout: post
title: CentOS 7 安装 JDK
date: 2015-05-13 02:41
author: admin
comments: true
categories: [CentOS,Java]
tags: [CentOS,Java]
---

## 环境

* CentOS 7.1 (64-bit system)
* JDK 8 (jdk-8u45-linux-x64.tar)

## CentOS 安装

参考：<http://www.waylau.com/centos-7-installation-and-configuration/>

<!-- more -->

## 下载 JDK

### 方法1:编译文件压缩包

地址为 <http://www.oracle.com/technetwork/java/javase/downloads/>
本例为 `jdk-8u45-linux-x64.tar`

### 方法2:yum 包

地址为 <http://download.oracle.com/otn-pub/java/jdk/8u45-b14/jdk-8u45-linux-x64.rpm> 本例为 `jdk-8u45-linux-x64.rpm`

## 安装

### 方法1:编译文件压缩包安装

#### 创建目录

home 目录下建 software目录（也可以是任意目录）下用于存放下载的软件。
    
    mkdir  /home/software
    
根目录下 usr 目录下新建 java 目录,用于 jdk 的安装路径。

    mkdir  /usr/java

#### 解压下载包

切换到 software 目录，里面有我们之前的 `jdk-8u45-linux-x64.tar`
执行

    tar zxvf jdk-8u45-linux-x64.gz
    
解压后，software 下就多了一个 jdk1.8.0_45 包

#### 移动到 java 安装目录下

    mv jdk1.8.0_45 /usr/java
    
### 方法2:yum 安装

执行 yum localinstall jdk-8u45-linux-x64.rpm

	[root@emsc software]# yum localinstall jdk-8u45-linux-x64.rpm
	已加载插件：fastestmirror
	正在检查 jdk-8u45-linux-x64.rpm: 2000:jdk1.8.0_45-1.8.0_45-fcs.x86_64
	jdk-8u45-linux-x64.rpm 将被安装
	正在解决依赖关系
	--> 正在检查事务
	---> 软件包 jdk1.8.0_45.x86_64.2000.1.8.0_45-fcs 将被 安装
	--> 解决依赖关系完成
	
	依赖关系解决
	
	================================================================================
	 Package         架构       版本                  源                       大小
	================================================================================
	正在安装:
	 jdk1.8.0_45     x86_64     2000:1.8.0_45-fcs     /jdk-8u45-linux-x64     233 M
	
	事务概要
	================================================================================
	安装  1 软件包
	
	总计：233 M
	安装大小：233 M
	Is this ok [y/d/N]:

输入 y 继续，

	Is this ok [y/d/N]: y
	Downloading packages:
	Running transaction check
	Running transaction test
	Transaction test succeeded
	Running transaction
	  正在安装    : 2000:jdk1.8.0_45-1.8.0_45-fcs.x86_64                        1/1
	Unpacking JAR files...
	        rt.jar...
	        jsse.jar...
	        charsets.jar...
	        tools.jar...
	        localedata.jar...
	        jfxrt.jar...
	        plugin.jar...
	        javaws.jar...
	        deploy.jar...
	  验证中      : 2000:jdk1.8.0_45-1.8.0_45-fcs.x86_64                        1/1
	
	已安装:
	  jdk1.8.0_45.x86_64 2000:1.8.0_45-fcs
	
	完毕！


##	配置环境变量

### 方法1

修改 profile 文件

    vi /etc/profile
    
在文件的末尾添加如下

  	export JAVA_HOME=/usr/java/jdk1.8.0_45
  	export PATH=$JAVA_HOME/bin:$PATH
    export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

注意

JAVA_HOME 是你的 JDK 安装目录

Linux 下用冒号“:”来分隔路径

$PATH / $CLASSPATH / $JAVA_HOME 是用来引用原来的环境变量的值

export 是把这三个变量导出为全局变量。

###	方法2

上述修改 放到 .bash_profile 文件末尾中

这种方法更为安全，它可以把使用这些环境变量的权限控制到用户级别，如果你需要给某个用户权限使用这些环境变量，你只需要修改其个人用户主目录下的.bash_profile文件就可以了。

##	测试

重新登录账号

执行  
  
    java –version
    
显示如下，说明安装成功

    java version "1.8.0_45"
    Java(TM) SE Runtime Environment (build 1.8.0_45-b14)
    Java HotSpot(TM) 64-Bit Server VM (build 25.45-b02, mixed mode)
