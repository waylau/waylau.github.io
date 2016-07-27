---
layout: post
title: 执行 Maven 编译的 jar,找不到相关的 依赖的类--使用 maven-assembly-plugin 解决
date: 2014-11-28 01:41
author: admin
comments: true
categories: [Maven]
tags: [Maven,jar]
---

##问题：执行 jar 找不到依赖的类

用 Maven 编译完成后 ，生产了 `ui-compressor-1.0.0.jar`, 此时执行 

	java -cp target/ui-compressor-1.0.0.jar com.waylau.uicompressor.App

提示下面找不到依赖的包：

	Exception in thread "main" java.lang.NoClassDefFoundError: org/mozilla/javascrip
	t/ErrorReporter
	        at java.lang.Class.getDeclaredMethods0(Native Method)
	        at java.lang.Class.privateGetDeclaredMethods(Class.java:2442)
	        at java.lang.Class.getMethod0(Class.java:2685)
	        at java.lang.Class.getMethod(Class.java:1620)
	        at sun.launcher.LauncherHelper.getMainMethod(LauncherHelper.java:492)
	        at sun.launcher.LauncherHelper.checkAndLoadMain(LauncherHelper.java:484)
	
	Caused by: java.lang.ClassNotFoundException: org.mozilla.javascript.ErrorReporte
	r
	        at java.net.URLClassLoader$1.run(URLClassLoader.java:366)
	        at java.net.URLClassLoader$1.run(URLClassLoader.java:355)
	        at java.security.AccessController.doPrivileged(Native Method)
	        at java.net.URLClassLoader.findClass(URLClassLoader.java:354)
	        at java.lang.ClassLoader.loadClass(ClassLoader.java:423)
	        at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:308)
	        at java.lang.ClassLoader.loadClass(ClassLoader.java:356)
	        ... 6 more

<!-- more -->

解压 jar  ,发现只有程序的主文件在，其他的依赖的包都不见，当然无法正常运行。

##解决：使用 maven-assembly-plugin

在项目 pom.xml 中添加 maven-assembly-plugin：
  
	<build>
		<!--使用Maven编译可执行的jar -->
		<plugins>
			<plugin>
				<artifactId>maven-assembly-plugin</artifactId>
				<configuration>
					<appendAssemblyId>false</appendAssemblyId>
					<descriptorRefs>
						<descriptorRef>jar-with-dependencies</descriptorRef>
					</descriptorRefs>
					<archive>
						<manifest>
							<mainClass>com.waylau.uicompressor.App</mainClass>
						</manifest>
					</archive>
				</configuration>
				<executions>
					<execution>
						<id>make-assembly</id>
						<phase>package</phase>
						<goals>
							<goal>assembly</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>

其中，`mainClass` 是你的程序主入口文件

编译、安装依赖

	mvn install

执行：

	java -cp target/ui-compressor-1.0.0.jar com.waylau.uicompressor.App

成功运行了

![](http://99btgc01.info/uploads/2014/11/mvn-jar.jpg)

## maven-assembly-plugin 介绍

### 什么是 maven-assembly-plugin

maven-assembly-plugin 插件主要是为了允许用户输出项目及其依赖关系、模块、网站文档和其他文件到一个单独的可发布的文档中。

目前支持发布的格式有：

* zip
* tar
* tar.gz (or tgz)
* tar.bz2 (or tbz2)
* jar
* dir
* war
* 及其他由 ArchiveManager 配置的任意格式

使用该插件只需三步：

1. 选择或者写入要使用的 [assembly descriptor](http://maven.apache.org/plugins/maven-assembly-plugin/descriptor-refs.html),
2. 配置 Assembly 插件到项目的 pom.xml,
3. 项目中运行 "mvn assembly:single"  

###如何使用 maven-assembly-plugin

####配置

将要使用  assembly descriptor 的，填入 `<descriptorRefs>/<descriptorRef>` 参数即可。如果使用自定义的  assembly descriptor 配置 你的 descriptor 路径到 `<descriptors>/<descriptor>` 参数。

注意装配的单个调用 Assembly 插件可以从多个 descriptor 产生 assembly,这样最大的灵活性允许您自定义项目的二进制文件项。

举例，现在项目要生产 JAR ，编译文件中要包含项目的依赖，配置如下：

	<project>
	  [...]
	  <build>
	    [...]
	    <plugins>
	      <plugin>
	        <!-- NOTE: We don't need a groupId specification because the group is
	             org.apache.maven.plugins ...which is assumed by default.
	         -->
	        <artifactId>maven-assembly-plugin</artifactId>
	        <version>2.5.2</version>
	        <configuration>
	          <descriptorRefs>
	            <descriptorRef>jar-with-dependencies</descriptorRef>
	          </descriptorRefs>
	        </configuration>
	        [...]
	</project>

注意 Assembly 插件允许你使用多个 `descriptorRefs`，产生不同的assembly.

另外,我们已经创建了一个自定义 descriptor 名字叫 `src.xml` 在  src/assembly ,可以这样使用：

	<project>
	  [...]
	  <build>
	    [...]
	    <plugins>
	      <plugin>
	        <artifactId>maven-assembly-plugin</artifactId>
	        <version>2.5.2</version>
	        <configuration>
	          <descriptors>
	            <descriptor>src/assembly/src.xml</descriptor>
	          </descriptors>
	        </configuration>
	        [...]
	</project>

####编译 Assembly

将 `single` 目标绑定到项目的生命周期，配置如下

	<project>
	  [...]
	  <build>
	    [...]
	    <plugins>
	      <plugin>
	        <artifactId>maven-assembly-plugin</artifactId>
	        <version>2.5.2</version>
	        <configuration>
	          <descriptorRefs>
	            <descriptorRef>jar-with-dependencies</descriptorRef>
	          </descriptorRefs>
	        </configuration>
	        <executions>
	          <execution>
	            <id>make-assembly</id> <!-- this is used for inheritance merges -->
	            <phase>package</phase> <!-- bind to the packaging phase -->
	            <goals>
	              <goal>single</goal>
	            </goals>
	          </execution>
	        </executions>
	      </plugin>
	      [...]
	</project>

打包：

	mvn package

当编译完成，target 目录下，生产了一个 jar

	target/sample-1.0-SNAPSHOT-jar-with-dependencies.jar

####高级配置

##### 创建一个可执行的 JAR

一旦您创建了这个独立的 JAR,就可以通过 -jar 在 JVM 运行。
	
通过 mainClass 来配置 JAR 中的 Main-Class 属性:

	<project>
	  [...]
	  <build>
	    [...]
	    <plugins>
	      <plugin>
	        <artifactId>maven-assembly-plugin</artifactId>
	        <version>2.5.2</version>
	        <configuration>
	          [...]
	          <archive>
	            <manifest>
	              <mainClass>org.sample.App</mainClass>
	            </manifest>
	          </archive>
	        </configuration>
	        [...]
	      </plugin>
	      [...]
	</project>

再次编译 META-INF/MANIFEST.MF 中可以看到
	
	[...]
	Main-Class: org.sample.App

注意只有  jar 和 war 的 assembly 格式支持`<archive>`配置


参考：

* [http://maven.apache.org/plugins/maven-assembly-plugin/](http://maven.apache.org/plugins/maven-assembly-plugin/)
 