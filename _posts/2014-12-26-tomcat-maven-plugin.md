---
layout: post
title:  Tomcat Maven Plugin使用
date: 2014-12-26 01:21
author: admin
comments: true
categories: [Tomcat]
tags: [Tomcat,Maven]
---

Apache Tomcat 的 Maven 插件提供了 goal，实现将 Apache Tomcat servlet 容器整合进 WAR 项目。你可以通过 Apache Maven 运行 WAR 项目而无需部署 WAR 文件到 Apache Tomcat 的实例。

<!-- more -->

详见下面网站：

* [Trunk](http://tomcat.apache.org/maven-plugin-trunk/)  (开发环境下)
* [Version 2.2](http://tomcat.apache.org/maven-plugin-2.2/) (2013-11-11)
* [Version 2.1](http://tomcat.apache.org/maven-plugin-2.1/) (2013-02-25)
* [Version 2.0](http://tomcat.apache.org/maven-plugin-2.0/) (2012-09-14)
* [Version 2.0-beta-1](http://tomcat.apache.org/maven-plugin-2.0-beta-1/) (2012-02-01）
 
##源码：

svn 路径：

<http://svn.apache.org/repos/asf/tomcat/maven-plugin/trunk/>

##功能：

* 支持 Apache Tomcat7 
* 构建一个可执行的 War/Jar

##使用

自从版本 2.0-beta-1以后， tomcat mojos 更名为  tomcat6 和 tomcat7，使用配置如下：

    <pluginManagement>
      <plugins>
        <plugin>
          <groupId>org.apache.tomcat.maven</groupId>
          <artifactId>tomcat6-maven-plugin</artifactId>
          <version>2.3-SNAPSHOT</version>
        </plugin>
        <plugin>
          <groupId>org.apache.tomcat.maven</groupId>
          <artifactId>tomcat7-maven-plugin</artifactId>
          <version>2.3-SNAPSHOT</version>
        </plugin>
      </plugins>
    </pluginManagement>

或者添加 groupId 到你的 settings.xm

	 <pluginGroups>
	    ....
	    <pluginGroup>org.apache.tomcat.maven</pluginGroup>
	    ....
	 </pluginGroups>

###在 tomcat 发布不显示项目名称

配置如下
	
	<plugin>
	  <groupId>org.apache.tomcat.maven</groupId>
	  <artifactId>tomcat7-maven-plugin</artifactId>
	  <version>2.2</version>
		<configuration>
		<path>/</path>
		</configuration>
	</plugin>

##Context Goals

###部署 WAR 项目

在 tomcat:deploy 部署后，再次部署 WAR 执行：

    mvn package tomcat6/7:redeploy

在执行 tomcat:exploded 后，再次部署 WAR 执行：

	mvn war:exploded tomcat6/7:redeploy

在执行 tomcat:inplace 后，再次部署 WAR 执行：

	mvn war:inplace tomcat6/7:redeploy

在执行 tomcat:deploy 后，再次部署 context.xml 文件执行：

	mvn tomcat6/7:redeploy

注意: `context.xml`指定 `docBase`后 ，还需要像上面那样调用 `war:exploded`或者`war:inplace` 

###移除部署 WAR 项目

	mvn tomcat6/7:undeploy

###启动 WAR 项目

	mvn tomcat6:start

###停止 WAR 项目

    mvn tomcat6:stop

###列出会话统计表

    mvn tomcat6:sessions

##Container Goals

###列出部署的应用s

	mvn tomcat6:list

###列出服务器信息

	mvn tomcat6:info

###列出 JNDI 资源

列出 Tomcat 版本, 操作系统, 和 JVM 等信息

	mvn tomcat6:resources

另外，列出特定类型你可以只输入 JNDI 资源类型:

	mvn -Dmaven.tomcat.type=my.class.name tomcat6:resources

###列出安全角色

	mvn tomcat6:roles

##构建一个可执行的 War/Jar

版本 2.0 开始，嵌入的 Apache Tomcat7 可以构建一个可执行的 War/Jar（只支持 tomcat7 插件）

注意：项目必须有  pom 或者  war 的打包信息

###添加到您的 war 模块：

	<project>
	  ...
	  <packaging>war or pom</packaging>
	  ...
	  <build>
	    ...
	    <plugins>
	      ...
	      <plugin>
	        <groupId>org.apache.tomcat.maven</groupId>
	        <artifactId>tomcat7-maven-plugin</artifactId>
	        <version>2.3-SNAPSHOT</version>
	        <executions>
	          <execution>
	            <id>tomcat-run</id>
	            <goals>
	              <goal>exec-war-only</goal>
	            </goals>
	            <phase>package</phase>
	            <configuration>
	              <path>foo</path>
	              <!-- optional, needed only if you want to use a preconfigured server.xml file -->
	              <serverXml>src/main/tomcatconf/server.xml</serverXml>
	              <!-- optional values which can be configurable -->
	              <attachArtifactClassifier>default value is exec-war but you can customize</attachArtifactClassifier>
	              <attachArtifactClassifierType>default value is jar</attachArtifactClassifierType>
	            </configuration>
	          </execution>
	        </executions>
	      </plugin>
	      ...
	    </plugins>
	    ...
	  </build>
	  ...
	</project>

###添加到 pom 模块

	<project>
	  ...
	  <packaging>war</packaging>
	  ...
	  <build>
	    ...
	    <plugins>
	      ...
	      <plugin>
	        <groupId>org.apache.tomcat.maven</groupId>
	        <artifactId>tomcat7-maven-plugin</artifactId>
	        <version>2.3-SNAPSHOT</version>
	        <executions>
	          <execution>
	            <id>tomcat-run</id>
	            <goals>
	              <goal>exec-war-only</goal>
	            </goals>
	            <phase>package</phase>
	            <configuration>
	              <!-- optional only if you want to use a preconfigured server.xml file -->
	              <!--
	              <serverXml>src/main/tomcatconf/server.xml</serverXml>
	              -->
	              <warRunDependencies>
	                <warRunDependency>
	                  <dependency>
	                    <groupId>a groupId</groupId>
	                    <artifactId>and artifactId</artifactId>
	                    <version>version</version>
	                    <type>war</type>
	                  </dependency>
	                  <contextPath>/</contextPath>
	                </warRunDependency>
	              </warRunDependencies>
	              <!-- naming is disabled by default so use true to enable it -->
	              <enableNaming>true</enableNaming>
	              <!-- extra dependencies to add jdbc driver, mail jars, etc. -->
	              <extraDependencies>
	                <extraDependency>
	                  <groupId>org.apache.derby</groupId>
	                  <artifactId>derby</artifactId>
	                  <version>10.1.3.1</version>
	                </extraDependency>
	                <extraDependency>
	                  <groupId>javax.mail</groupId>
	                  <artifactId>mail</artifactId>
	                  <version>1.4</version>
	                </extraDependency>
	              </extraDependencies>
	            </configuration>
	          </execution>
	        </executions>
	      </plugin>
	      ...
	    </plugins>
	    ...
	  </build>
	  ...
	</project>

###生成可执行的 War/Jar

在上面配置下，执行

	java -jar yourjar

下面是使用帮助

	usage: java -jar [path to your exec war jar]
	 -ajpPort <ajpPort>                     ajp port to use
	 -clientAuth                            enable client authentication for
	                                        https
	 -D <arg>                               key=value
	 -extractDirectory <extractDirectory>   path to extract war content,
	                                        default value: .extract
	 -h,--help                              help
	 -httpPort <httpPort>                   http port to use
	 -httpProtocol <httpProtocol>           http protocol to use: HTTP/1.1 or
	                                        org.apache.coyote.http11.Http11Nio
	                                        Protocol
	 -httpsPort <httpsPort>                 https port to use
	 -maxPostSize <maxPostSize>             max post size in bytes to use
	 -keyAlias <keyAlias>                   alias from keystore for ssl
	 -loggerName <loggerName>               logger to use: slf4j to use slf4j
	                                        bridge on top of jul
	 -obfuscate <password>                  obfuscate the password and exit
	 -resetExtract                          clean previous extract directory
	 -serverXmlPath <serverXmlPath>         server.xml to use, optional
	 -X,--debug                             debug

##已知限制

一些 goal　不在　tomcat7 mojo。　这些在 tomcat6 mojo 中的　[container goal](http://tomcat.apache.org/maven-plugin-trunk/container-goals.html)，需要修改你的 manager url ：

使用 `http://localhost:8080/manager/text` 替换默认的 tomcat6 url


参考：

* <http://tomcat.apache.org/maven-plugin.html>
