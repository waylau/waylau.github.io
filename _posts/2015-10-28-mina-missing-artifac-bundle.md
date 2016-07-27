---
layout: post
title: 解决 Missing artifact org.apache.mina:mina-core:bundle:2.0.9
date: 2015-10-28 01:08
author: admin
comments: true
categories: [Maven,MINA]
tags: [Maven,MINA]
---

Maven 项目中引用了 MINA 的依赖，包如下错误：

	Missing artifact org.apache.mina:mina-core:bundle:2.0.9

<!-- more -->

## 依赖

项目引入的 MINA 依赖如下：

	<dependency>
		<groupId>org.apache.mina</groupId>
		<artifactId>mina-core</artifactId>
		<version>${version.mina}</version>
	</dependency>
	<dependency>
		<groupId>org.apache.mina</groupId>
		<artifactId>mina-integration-jmx</artifactId>
		<version>${version.mina}</version>
	</dependency>
	<dependency>
		<groupId>org.apache.mina</groupId>
		<artifactId>mina-integration-beans</artifactId>
		<version>${version.mina}</version>
	</dependency>
	<dependency>
		<groupId>org.apache.mina</groupId>
		<artifactId>mina-integration-ognl</artifactId>
		<version>${version.mina}</version>
	</dependency>
   
## 解决

在 pom.xml 中添加 如下插件：

	<build>
		<plugins>
			...
			<plugin>
				<groupId>org.apache.felix</groupId>
				<artifactId>maven-bundle-plugin</artifactId>
				<extensions>true</extensions>
			</plugin>
		</plugins>
	</build>


## 参考

* 源码见：<https://github.com/waylau/apache-mina-2-user-guide-demos>
* [Apache MINA 2 用户指南](https://github.com/waylau/apache-mina-2.x-user-guide)
* <http://felix.apache.org/documentation/subprojects/apache-felix-maven-bundle-plugin-bnd.html>