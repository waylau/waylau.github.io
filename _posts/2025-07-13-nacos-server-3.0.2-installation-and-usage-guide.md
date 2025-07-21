---
layout: post
title: Nacos Server 3.0.2在Windows下的安装使用
date: 2025-07-13 00:22
author: admin
comments: true
categories: [Nacos]
tags: [Nacos]
---

本文主要介绍在Windows系统下通过ZIP文件安装Nacos Server 3.0.2。

<!-- more -->

## 下载安装包

从Nacos官方（<https://nacos.io/download/nacos-server/>）下载3.0.2版本编译文件压缩包（如`nacos-server-3.0.2.zip`）。


解压至目标目录（如`D:\dev\java\nacos-server-3.0.2\nacos`）。



## 配置和启动Nacos


### 配置Nacos

application.properties配置如下：

```yml
nacos.core.auth.server.identity.key=admin
nacos.core.auth.server.identity.value=admin123

nacos.core.auth.plugin.nacos.token.secret.key=bQUBj9U7io0VXuhlaC9XmeaSGSwkqOlG4itHzIgUvOk

nacos.console.port=8280
```



在Nacos中配置nacos.core.auth.server.identity.key是为了实现服务器身份验证的一部分，它与nacos.core.auth.server.identity.value共同构成一个键值对，用于标识Nacos服务器的身份。这一配置尤其重要，当你开启了Nacos的鉴权功能时，以确保只有合法的服务器能参与集群工作，增加安全性。

在配置Nacos的鉴权参数nacos.core.auth.plugin.nacos.token.secret.key时，应遵循以下建议以确保系统的安全性：

生成强密码: 使用一个自定义的Base64编码字符串作为该参数的值。此密钥将用于加密生成的令牌，确保其强度是至关重要的。原始密钥长度不得低于32字符。可以通过在线工具如此网站生成一个大于32位的文本字符，然后将其Base64编码作为配置值。

确保唯一性与一致性: 在所有Nacos集群节点上，该配置值必须保持一致。这意味着，当你在一台服务器上更改了此配置，所有其他节点也需要做相应的更新，以维持集群内部的一致性。

控制台默认端口是8082，可以自定义，例如本例改为了8280。


### 启动Nacos


在Windows下，执行如下命令启动：

```cmd
cd D:\dev\java\nacos-server-3.0.2\nacos\bin
.\startup.cmd -m standalone
```

### 验证Nacos Server端


看到如下日志，则证明启动成功：

```

2025-07-13 22:28:09,198 INFO Nacos started successfully in stand alone mode with embedded storage in 17608 ms


         ,--.
       ,--.'|
   ,--,:  : |                                           Nacos Server API 3.0.2
,`--.'`|  ' :                       ,---.               Running in stand alone mode, All function modules
|   :  :  | |                      '   ,'\   .--.--.    Port: 8848
:   |   \ | :  ,--.--.     ,---.  /   /   | /  /    '   Pid: 22260
|   : '  '; | /       \   /     \.   ; ,. :|  :  /`./
'   ' ;.    ;.--.  .-. | /    / ''   | |: :|  :  ;_
|   | | \   | \__\/: . ..    ' / '   | .; : \  \    `.      https://nacos.io
'   : |  ; .' ," .--.; |'   ; :__|   :    |  `----.   \
|   | '`--'  /  /  ,.  |'   | '.'|\   \  /  /  /`--'  /
'   : |     ;  :   .'   \   :    : `----'  '--'.     /
;   |.'     |  ,     .-./\   \  /            `--'---'
'---'        `--`---'     `----'

2025-07-13 22:28:10,329 INFO Nacos Server API is starting...

2025-07-13 22:28:11,330 INFO Nacos Server API is starting...

2025-07-13 22:28:11,755 WARN Bean 'nacosWebBeanPostProcessorConfiguration' of type [com.alibaba.nacos.server.NacosWebBeanPostProcessorConfiguration$$SpringCGLIB$$0] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying). The currently created BeanPostProcessor [nacosDuplicateSpringBeanPostProcessor] is declared through a non-static factory method on that class; consider declaring it as static instead.

2025-07-13 22:28:12,332 INFO Nacos Server API is starting...

2025-07-13 22:28:12,450 INFO Tomcat initialized with port 8848 (http)

2025-07-13 22:28:12,582 INFO Root WebApplicationContext: initialization completed in 3241 ms

2025-07-13 22:28:13,334 INFO Nacos Server API is starting...

2025-07-13 22:28:13,393 INFO Adding welcome page: class path resource [static/index.html]

2025-07-13 22:28:14,304 INFO Exposing 1 endpoint beneath base path '/actuator'

2025-07-13 22:28:14,336 INFO Nacos Server API is starting...

2025-07-13 22:28:14,397 INFO Tomcat started on port 8848 (http) with context path '/nacos'

2025-07-13 22:28:14,433 INFO Nacos Server API started successfully in 5210 ms


         ,--.
       ,--.'|
   ,--,:  : |                                           Nacos Console 3.0.2
,`--.'`|  ' :                       ,---.               Running in stand alone mode, All function modules
|   :  :  | |                      '   ,'\   .--.--.    Port: 8082
:   |   \ | :  ,--.--.     ,---.  /   /   | /  /    '   Pid: 22260
|   : '  '; | /       \   /     \.   ; ,. :|  :  /`./   Console: http://192.168.186.124:8082/index.html
'   ' ;.    ;.--.  .-. | /    / ''   | |: :|  :  ;_
|   | | \   | \__\/: . ..    ' / '   | .; : \  \    `.      https://nacos.io
'   : |  ; .' ," .--.; |'   ; :__|   :    |  `----.   \
|   | '`--'  /  /  ,.  |'   | '.'|\   \  /  /  /`--'  /
'   : |     ;  :   .'   \   :    : `----'  '--'.     /
;   |.'     |  ,     .-./\   \  /            `--'---'
'---'        `--`---'     `----'

2025-07-13 22:28:15,210 WARN Bean 'nacosConsoleBeanPostProcessorConfiguration' of type [com.alibaba.nacos.console.config.NacosConsoleBeanPostProcessorConfiguration$$SpringCGLIB$$0] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying). The currently created BeanPostProcessor [nacosDuplicateSpringBeanPostProcessor] is declared through a non-static factory method on that class; consider declaring it as static instead.

2025-07-13 22:28:15,329 INFO Tomcat initialized with port 8082 (http)

2025-07-13 22:28:15,343 INFO Root WebApplicationContext: initialization completed in 841 ms

2025-07-13 22:28:15,499 INFO Nacos Console is starting...

2025-07-13 22:28:15,627 INFO Adding welcome page: class path resource [static/index.html]

2025-07-13 22:28:16,107 INFO Exposing 1 endpoint beneath base path '/actuator'

2025-07-13 22:28:16,144 INFO Tomcat started on port 8082 (http) with context path '/'

2025-07-13 22:28:16,156 INFO Nacos Console started successfully in 1704 ms
```



## 访问Web界面

浏览器打开`http://localhost:8082`，进入Nacos的Web界面。


首次登录，需要设置nacos管理员密码，比如`admin123`。


## 在Spring Cloud Alibaba里面使用


POM.xml核心配置如下：


```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>3.3.13</version> <!-- 为了适配 Spring Cloud Alibaba-->
  <relativePath/> <!-- lookup parent from repository -->
</parent>

<properties>
  <java.version>17</java.version>
  <spring-cloud-alibaba.version>2023.0.3.3</spring-cloud-alibaba.version>
</properties>

<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
  </dependency>

</dependencies>

<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>com.alibaba.cloud</groupId>
      <artifactId>spring-cloud-alibaba-dependencies</artifactId>
      <version>${spring-cloud-alibaba.version}</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

Spring Cloud Alibaba目前最新版本是2023.0.3.3，最高只能支持到Spring Boot 3.3.13，以及JDK 21。

在应用的Application中添加`@EnableDiscoveryClient`：


```java
package com.waylau.rednote.filesmicroservice;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```


应用配置里面添加;

```
# 配置 Nacos
spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
management.endpoints.web.exposure.include=*
```


应用启动之后，就能在Nacos管理界面看到服务的状态了。

![访问Nacos管理界面](/images/post/20250713-nacos-001.png)




## 参考引用



* https://nacos.io/docs/latest/ecology/use-nacos-with-spring-cloud/?spm=5238cd80.2ef5001f.0.0.3f613b7cT31fzW