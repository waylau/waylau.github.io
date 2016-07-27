---
layout: post
title: 用 Docker 构建、运行、发布一个 Spring Boot 应用
date: 2016-03-20 01:41
author: admin
comments: true
categories: [Docker,Spring Boot]
tags: [Docker,Spring Boot,镜像,Docker Hub]
---

本文演示了如何用 Docker 构建、运行、发布来一个 Spring Boot 应用。

<!-- more -->

## Docker 简介

[Docker](https://docker.com/) 是一个 Linux 容器管理工具包，具备“社交”方面，允许用户发布容器的 image (镜像)，并使用别人发布的 image。Docker image 是用于运行容器化进程的方案，在本文中，我们将构建一个简单的 Spring Boot 应用程序。

有关 Docker 的详细介绍，可以移步至 [《简述 Docker》](http://waylau.com/ahout-docker/)

## 前置条件

* [JDK 1.8+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Maven 3.0+](http://maven.apache.org/download.cgi)
* Docker 最新版。有关 Docker 在的安装，可以参阅 [《Docker 在 CentOS 下的安装、使用》](http://waylau.com/docker-installation-centos/)。
如果你的电脑不是 Linux 系统，最好装个虚拟机，在虚拟机里面装个 Linux ，因为 Docker 的依赖 Linux。

## 用 Maven 构建项目

### 创建目录结构

项目的目录结构因符合 Maven 的约定。

在 *nix 系统下执行 `mkdir -p src/main/java/docker_spring_boot` ,生产如下结构 :

```
└── src
    └── main
        └── java
            └── com
                └── waylau
                    └── docker_spring_boot
```

### 创建 pom.xml 文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

	<modelVersion>4.0.0</modelVersion>

	<groupId>com.waylau</groupId>
	<artifactId>docker-spring-boot</artifactId>
	<packaging>jar</packaging>
	<version>1.0.0</version>
	<name>docker-spring-boot</name>
	<description>Getting started with Spring Boot and Docker</description>
	
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.3.3.RELEASE</version>
        <relativePath/>
    </parent>

	<dependencies>
	    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
	</dependencies>

	<build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
    <!-- tag::plugin[] -->
			<plugin>
				<groupId>com.spotify</groupId>
				<artifactId>docker-maven-plugin</artifactId>
				<version>0.4.3</version>
				<configuration>
					<imageName>${docker.image.prefix}/${project.artifactId}</imageName>
					<dockerDirectory>src/main/docker</dockerDirectory>
					<resources>
						<resource>
							<targetPath>/</targetPath>
							<directory>${project.build.directory}</directory>
							<include>${project.build.finalName}.jar</include>
						</resource>
					</resources>
				</configuration>
			</plugin>
    <!-- end::plugin[] -->
        </plugins>
	</build>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<docker.image.prefix>waylau</docker.image.prefix>
		<spring.boot.version>1.3.3.RELEASE</spring.boot.version>
	</properties>
</project>


```

[Spring Boot Maven plugin](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-tools/spring-boot-maven-plugin) 提供了很多方便的功能：

* 它收集的类路径上所有 jar 文件，并构建成一个单一的、可运行的“über-jar”，这使得它更方便地执行和传输服务。
* 它搜索的 `public static void main()` 方法来标记为可运行的类。
* 它提供了一个内置的依赖解析器，用于设置版本号以匹配 [Spring Boot 的依赖](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-dependencies/pom.xml)。您可以覆盖任何你想要的版本，但它会默认选择的 Boot 的版本集。

Spotify 的 [docker-maven-plugin](https://github.com/spotify/docker-maven-plugin) 插件是用于构建 Maven 的 Docker Image

* `imageName`指定了镜像的名字，本例为  `waylau/docker-spring-boot`
* `dockerDirectory`指定 Dockerfile 的位置
* `resources`是指那些需要和 Dockerfile 放在一起，在构建镜像时使用的文件，一般应用 jar 包需要纳入。本例，只需一个 jar 文件。


## 编写 Spring Boot 应用

编写一个简单的 Spring Boot 应用 ：

`src/main/java/com/waylau/docker_spring_boot/Application.java`:

```java
package com.waylau.docker_spring_boot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
 
/**
 * 主应用入口
 * @author <a href="http://waylau.com">waylau.com</a>
 * @date 2016年3月19日
 */
@SpringBootApplication
@RestController
public class Application {

	@RequestMapping("/")
	public String home() {
		return "Hello Docker World."
				+ "<br />Welcome to <a href='http://waylau.com'>waylau.com</a></li>";
	}

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```

解释下上面的代码：

* 类用 `@SpringBootApplication` `@RestController` 标识,可用 Spring MVC 来处理 Web 请求。 
* `@RequestMapping` 将 `/` 映射到 `home()` ，并将"Hello Docker World" 文本作为响应。
* `main()` 方法使用 Spring Boot 的 `SpringApplication.run()` 方法来启动应用。

## 运行程序

### 使用 Maven

编译：

    mvn package

运行：

    java -jar target/docker-spring-boot-1.0.0.jar

### 访问项目

如果程序正确运行，浏览器访问 <http://localhost:8080/>，可以看到页面 “Hello Docker World.” 字样。

## 将项目容器化

Docker 使用 [Dockerfile](https://docs.docker.com/reference/builder/) 文件格式来指定 image 层，

创建文件 `src/main/docker/Dockerfile`:

```
FROM frolvlad/alpine-oraclejdk8:slim
VOLUME /tmp
ADD docker-spring-boot-1.0.0.jar app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

解释下这个配置文件：

* `VOLUME` 指定了临时文件目录为`/tmp`。其效果是在主机 `/var/lib/docker` 目录下创建了一个临时文件，并链接到容器的`/tmp`。改步骤是可选的，如果涉及到文件系统的应用就很有必要了。`/tmp`目录用来持久化到 Docker 数据文件夹，因为 Spring Boot 使用的内嵌 Tomcat 容器默认使用`/tmp`作为工作目录
* 项目的 jar 文件作为 "app.jar" 添加到容器的
* `ENTRYPOINT` 执行项目 app.jar。为了缩短 [Tomcat 启动时间](http://wiki.apache.org/tomcat/HowTo/FasterStartUp#Entropy_Source)，添加一个系统属性指向 "/dev/urandom" 作为 Entropy Source

## 构建 Docker Image

执行构建成为 docker image:

    mvn package docker:build

## 运行 

运行 Docker Image

    docker run -p 8080:8080 -t waylau/docker-spring-boot

```
[root@waylau spring-boot]# docker run -p 8080:8080 -t waylau/docker-spring-boot

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.3.3.RELEASE)

2016-03-20 08:45:51.276  INFO 1 --- [           main] c.waylau.docker_spring_boot.Application  : Starting Application v1.0.0 on 048fb623038f with PID 1 (/app.jar started by root in /)
2016-03-20 08:45:51.289  INFO 1 --- [           main] c.waylau.docker_spring_boot.Application  : No active profile set, falling back to default profiles: default
2016-03-20 08:45:51.722  INFO 1 --- [           main] ationConfigEmbeddedWebApplicationContext : Refreshing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@669af5fc: startup date [Sun Mar 20 08:45:51 GMT 2016]; root of context hierarchy
2016-03-20 08:45:54.874  INFO 1 --- [           main] o.s.b.f.s.DefaultListableBeanFactory     : Overriding bean definition for bean 'beanNameViewResolver' with a different definition: replacing [Root bean: class [null]; scope=; abstract=false; lazyInit=false; autowireMode=3; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=org.springframework.boot.autoconfigure.web.ErrorMvcAutoConfiguration$WhitelabelErrorViewConfiguration; factoryMethodName=beanNameViewResolver; initMethodName=null; destroyMethodName=(inferred); defined in class path resource [org/springframework/boot/autoconfigure/web/ErrorMvcAutoConfiguration$WhitelabelErrorViewConfiguration.class]] with [Root bean: class [null]; scope=; abstract=false; lazyInit=false; autowireMode=3; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=org.springframework.boot.autoconfigure.web.WebMvcAutoConfiguration$WebMvcAutoConfigurationAdapter; factoryMethodName=beanNameViewResolver; initMethodName=null; destroyMethodName=(inferred); defined in class path resource [org/springframework/boot/autoconfigure/web/WebMvcAutoConfiguration$WebMvcAutoConfigurationAdapter.class]]
2016-03-20 08:45:57.893  INFO 1 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat initialized with port(s): 8080 (http)
2016-03-20 08:45:57.982  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service Tomcat
2016-03-20 08:45:57.984  INFO 1 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.0.32
2016-03-20 08:45:58.473  INFO 1 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2016-03-20 08:45:58.473  INFO 1 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 6877 ms
2016-03-20 08:45:59.672  INFO 1 --- [ost-startStop-1] o.s.b.c.e.ServletRegistrationBean        : Mapping servlet: 'dispatcherServlet' to [/]
2016-03-20 08:45:59.695  INFO 1 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'characterEncodingFilter' to: [/*]
2016-03-20 08:45:59.701  INFO 1 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
2016-03-20 08:45:59.703  INFO 1 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'httpPutFormContentFilter' to: [/*]
2016-03-20 08:45:59.703  INFO 1 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'requestContextFilter' to: [/*]
2016-03-20 08:46:00.862  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@669af5fc: startup date [Sun Mar 20 08:45:51 GMT 2016]; root of context hierarchy
2016-03-20 08:46:01.166  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/]}" onto public java.lang.String com.waylau.docker_spring_boot.Application.home()
2016-03-20 08:46:01.189  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2016-03-20 08:46:01.190  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
2016-03-20 08:46:01.302  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2016-03-20 08:46:01.302  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2016-03-20 08:46:01.438  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2016-03-20 08:46:01.833  INFO 1 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2016-03-20 08:46:02.332  INFO 1 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2016-03-20 08:46:02.343  INFO 1 --- [           main] c.waylau.docker_spring_boot.Application  : Started Application in 13.194 seconds (JVM running for 15.828)
```

### 访问项目

如果程序正确运行，浏览器访问 <http://localhost:8080/>，可以看到页面 “Hello Docker World.” 字样。

## 推送 image 到 Docker Hub


首先，你在  Docker Hub 要有注册账号，且创建了相应的库；

其次，docker 推送前，先要登录，否则报`unauthorized: access to the requested resource is not authorized`的错误

执行：

    docker login

输出为：

```
[root@waylau spring-boot]# docker login
Username: waylau
Password: 
Email: waylau521@gmail.com
WARNING: login credentials saved in /root/.docker/config.json
Login Succeeded
```

执行推送

    docker push waylau/docker-spring-boot

```
[root@waylau spring-boot]# docker push waylau/docker-spring-boot
The push refers to a repository [docker.io/waylau/docker-spring-boot]
751d29eef02e: Layer already exists 
4da3741f39c7: Pushed 
5f70bf18a086: Layer already exists 
7e4d0cb13643: Layer already exists 
8f045733649f: Layer already exists 
latest: digest: sha256:eb4d5308ba1bb27489d808279e74784bda6761b3574f4298d746abba59b35164 size: 9415
```


## 镜像加速器

Docker Hub 在国外，有时候拉取 Image 极其缓慢，可以使用国内的镜像来实现加速

### 阿里云

```
echo "DOCKER_OPTS=\"--registry-mirror=https://yourlocation.mirror.aliyuncs.com\"" | sudo tee -a /etc/default/docker
sudo service docker restart
```

其中 <https://yourlocation.mirror.aliyuncs.com> 是您在阿里云注册后的专属加速器地址：

### DaoCloud

```
sudo echo “DOCKER_OPTS=\”\$DOCKER_OPTS –registry-mirror=http://your-id.m.daocloud.io -d\”” >> /etc/default/docker
sudo service docker restart
```

其中 <http://your-id.m.daocloud.io> 是您在 DaoCloud 注册后的专属加速器地址：

## 源码

获取项目源码，
<https://github.com/waylau/docker-demos> 中的 `samples/spring-boot`

获取项目镜像，
执行

    docker pull waylau/docker-spring-boot

## 参考引用

* <http://spring.io/guides/gs/spring-boot-docker/>
* <https://yq.aliyun.com/articles/2930?utm_source=tuicool&utm_medium=referral>
* <https://hub.docker.com/r/waylau/docker-spring-boot/>
