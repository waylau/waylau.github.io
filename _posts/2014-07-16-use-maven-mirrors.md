---
layout: post
title: 使用maven镜像
date: 2014-07-16 07:08
author: admin
comments: true
categories: [Maven]
tags: [Maven,mirror]
---
 
#综述
用maven做项目，最郁闷的莫过于某些依赖库下载不了。被墙了，你懂的。使用maven镜像仓库及其重要，特别是国内的镜像，可以有效缓解被墙疼痛。

#常用的镜像
##国外镜像
ibiblio.org

	<mirror>  
	     <id>ibiblio</id>  
	     <mirrorOf>central</mirrorOf>  
	     <name>ibiblio Mirror of http://repo1.maven.org/maven2/</name>  
	     <url>http://mirrors.ibiblio.org/pub/mirrors/maven2/</url>  
	</mirror>  

jboss

	<mirror>  
	     <id>jboss-public-repository-group</id>  
	     <mirrorOf>central</mirrorOf>  
	     <name>JBoss Public Repository Group</name>  
	     <url>http://repository.jboss.org/nexus/content/groups/public</url>  
	</mirror>

repo2

	<mirror>
      <id>repo2</id>
      <mirrorOf>central</mirrorOf>
      <name>Human Readable Name for this Mirror.</name>
      <url>http://repo2.maven.org/maven2/</url>
    </mirror>

uk.maven.org

	<mirror>
      <id>ui</id>
      <mirrorOf>central</mirrorOf>
      <name>Human Readable Name for this Mirror.</name>
     <url>http://uk.maven.org/maven2/</url>
    </mirror>

##国内镜像
oschina.net

 	<mirror>
        <id>nexus-osc</id>
        <mirrorOf>*</mirrorOf>
        <name>Nexus osc</name>
        <url>http://maven.oschina.net/content/groups/public/</url>
    </mirror>

net.cn

	<mirror>
	    <id>net-cn</id>
	    <mirrorOf>central</mirrorOf>
	    <name>Human Readable Name for this Mirror.</name>
	    <url>http://maven.net.cn/content/groups/public/</url>	
	 </mirror>

#使用镜像
下文以oschina.net的镜像为例子.

1.Maven 的安装目录下的 conf 文件下有个`settings.xml`文件,编辑该文件

2.在&lt;mirrors&gt;中插入:

 	<mirror>
        <id>nexus-osc</id>
        <mirrorOf>*</mirrorOf>
        <name>Nexus osc</name>
        <url>http://maven.oschina.net/content/groups/public/</url>
    </mirror>

3.这里是配置 Maven 的 mirror 地址指向OSChina 的 Maven 镜像地址。 在执行 Maven 命令的时候， Maven 还需要安装一些插件包，这些插件包的下载地址也让其指向 oschina.net 的 Maven 地址。在&lt;profiles&gt;中插入:

	<profile>
	    <id>jdk-1.4</id>
	    <activation>
	    <jdk>1.4</jdk>
	    </activation>
	    <repositories>
	        <repository>
	            <id>nexus</id>
	            <name>local private nexus</name>
	            <url>http://maven.oschina.net/content/groups/public/</url>
	            <releases>
	                <enabled>true</enabled>
	            </releases>
	            <snapshots>
	                <enabled>false</enabled>
	            </snapshots>
	        </repository>
	    </repositories>
	    <pluginRepositories>
	        <pluginRepository>
	            <id>nexus</id>
	            <name>local private nexus</name>
	            <url>http://maven.oschina.net/content/groups/public/</url>
	            <releases>
	                <enabled>true</enabled>
	            </releases>
	            <snapshots>
	                <enabled>false</enabled>
	            </snapshots>
	        </pluginRepository>
	    </pluginRepositories>
	</profile>


参考：[http://maven.oschina.net/help.html](http://maven.oschina.net/help.html)