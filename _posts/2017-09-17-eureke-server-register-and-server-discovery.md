---
layout: post
title: 使用 Eureka 实现服务注册与发现
date: 2017-09-17 00:22
author: admin
comments: true
categories: [Eureka,Spring Cloud]
tags: [Eureka,Spring Cloud,Netflix]
---

Eureka 是 [Netflix](https://www.netflix.com) 出品的用于实现服务注册和发现的工具。 Spring Cloud 集成了 Eureka，并提供了开箱即用的支持。其中， Eureka 又可细分为 Eureka Server 和  Eureka Client。

本例子将演示如何通过  Spring Cloud Eureka 来快速实现服务的注册和发现。

<!-- more -->

## 开发环境

* Gradle 4.0
* Spring Boot 2.0.0.M3
* Spring Cloud Netflix Eureka Server Finchley.M2
* Spring Cloud Netflix Eureka Client Finchley.M2

## 从 Spring Initializr 进行项目的初始化

访问<http://start.spring.io/> 进行项目的初始化。我们将该项目命名为`micro-weather-eureka-server`。

![eurake-start](https://github.com/waylau/spring-cloud-tutorial/raw/master/images/register-discover/eurake-start.jpg)

## 更改配置

根据下面两个博客的指引来配置，加速项目的构建。

* Gradle Wrapper 引用本地的发布包 ： <https://waylau.com/change-gradle-wrapper-distribution-url-to-local-file/>
* 使用Maven镜像 ： <https://waylau.com/use-maven-mirrors/>

## 启用 Eureka Server

为启用 Eureka Server ，在 Application 上增加`@EnableEurekaServer`注解即可。

```java
@SpringBootApplication
@EnableEurekaServer
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
```

## 修改项目配置

修改 application.properties，增加如下配置。

```
server.port: 8761

eureka.instance.hostname: localhost
eureka.client.registerWithEureka: false
eureka.client.fetchRegistry: false
eureka.client.serviceUrl.defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

其中：

* server.port: 指明了应用启动的端口号
* eureka.instance.hostname: 应用的主机名称
* eureka.client.registerWithEureka: 值为`false`意味着自身仅作为服务器，不作为客户端 
* eureka.client.fetchRegistry:  值为`false`意味着无需注册自身
* eureka.client.serviceUrl.defaultZone: 指明了应用的URL

## 启动 Eureka Server

启动应用，访问<http://localhost:8761/>，可以看到  Eureka Server 自带的 UI 管理界面。

![eurake-ui](https://github.com/waylau/spring-cloud-tutorial/raw/master/images/register-discover/eurake-ui.jpg)


## 创建 Eureka Client

我们在`micro-weather-eureka-server`基础上，将创建一个`micro-weather-eureka-client` 作为客户端，并演示如何让将自身向注册服务器进行注册，让其可以其他服务都调用。
 

## 更改配置

增加如下配置：

```groovy
dependencies {
    //...

	compile('org.springframework.cloud:spring-cloud-starter-netflix-eureka-client')

	//...
}
```

## 一个最简单的 Eureka Client

```java
@SpringBootApplication
@EnableDiscoveryClient
@RestController
public class Application {

    @RequestMapping("/hello")
    public String home() {
        return "Hello world";
    }

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
```

其中`@EnableDiscoveryClient`启用了服务发现的功能，只要 Eureka Client 启动了，就能被  Eureka Server 所感知。

项目配置：

```
spring.application.name: micro-weather-eureka-client

eureka.client.serviceUrl.defaultZone: http://localhost:8761/eureka/
```

## 运行

分别在 8081 和 8082 上启动了客户端示例。

```java
java -jar micro-weather-eureka-client-1.0.0.jar --server.port=8081

java -jar micro-weather-eureka-client-1.0.0.jar --server.port=8082
```

可以在 Eureka Server 上看到这两个实体的信息。

![eurake-client](https://github.com/waylau/spring-cloud-tutorial/raw/master/images/register-discover/eurake-client.jpg)

## 源码

本章节源码，见<https://github.com/waylau/spring-cloud-tutorial>  samples 目录下的`micro-weather-eureka-server` 和 `micro-weather-eureka-client` 。
