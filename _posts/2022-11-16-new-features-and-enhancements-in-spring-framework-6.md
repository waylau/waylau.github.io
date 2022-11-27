---
layout: post
title: Spring Framework 6中的新功能和增强功能
date: 2022-11-16 00:22
author: admin
comments: true
categories: [Spring]
tags: [Spring]
---

在2022年11月16日，Juergen Hoeller发布了消息，称 Spring Framework 6将正式发布。Spring 6 是一个重要的版本，距离 Spring 5 发布有四年多了。通过本文的介绍，我们一起来快速了解 Spring 6发行版中的那些令人兴奋的特性。

<!-- more -->

## JDK 17+和Jakarta EE 9+基线

* 现在基于Java 17源代码级别的整个框架代码库。
* Servlet、JPA等从javax迁移到Jakarta 命名空间。
* 运行时与Jakarta EE 9以及Jakarta EE 10 API的兼容性。
* 与最新的Web服务器兼容：Tomcat 10.1，Jetty 11，Undertow 2.3.
* 早期兼容虚拟线程（从JDK 19开始预览）。

## 一般核心修订

* 升级到ASM 9.4和Kotlin 1.7。
* 完整的CGLIB fork，支持捕获CGLIB生成的类。
* 全面的向AOT（Ahead-Of-Time Processing，提前处理）转型。
* 对GraalVM原生映像的一流支持。

## 核心容器

* 默认情况下，无需java.beans.Introspector来确定基本bean属性。
* 在GenericApplicationContext (refreshForAotProcessing)中的支持AOT处理。
* 基于预解析构造函数和工厂方法的Bean定义转换。
* 支持AOP代理和配置类的早期代理类确定。
* PathMatchingResourcePatternResolver使用NIO和模块路径API进行扫描，分别支持GraalVM本机映像和Java模块路径中的类路径扫描。
* DefaultFormattingConversionService支持基于ISO的默认java.time类型解析。

## 数据访问和事务

* 支持预定JPA托管类型（用于包含在AOT处理中）。
* JPA支持Hibernate ORM 6.1（保持与Hibernate ORM 5.6的兼容性）。
* 升级到R2DBC 1.0（包括R2DBC事务定义）。
* 删除JCA CCI支持。

## Spring消息传递

* 基于@RSocketExchange服务接口的RSocket接口客户端。
* 基于Netty 5 Alpha的Reactor Netty 2的早期支持。
* 支持Jakarta WebSocket 2.1及其标准WebSocket协议升级机制。

## 通用Web修订

* 基于@HttpExchange服务接口的HTTP接口客户端。
* 支持RFC 7807问题详细信息.
* 统一HTTP状态码处理。
* 支持Jackson 2.14。
* 与Servlet 6.0对齐（同时保留与Servlet 5.0的运行时兼容性）。

## Spring MVC

* 默认情况下使用的PathPatternParser（能够选择进入PathMatcher）。
* 删除过时的Tiles和FreeMarker JSP支持。

## Spring WebFlux

* 新的PartEvent API用于流式传输多部分表单上传(两者都在客户端和服务器).
* 新的ResponseEntityExceptionHandler用于自定义WebFlux异常并呈现RFC 7807错误响应.
* 非流媒体类型的Flux返回值（写入前不再收集到List）。
* 基于Netty 5 Alpha的Reactor Netty 2的早期支持。
* JDK HttpClient与WebClient集成。

## 可观察性

* Micrometer Observation直接可观察性在Spring框架中的部分应用。spring-web模块现在需要io.micrometer:micrometer-observation:1.10+作为编译依赖项。
* RestTemplate和WebClient被检测为生成HTTP客户端请求观察。
* Spring MVC可以使用新的org.springframework.web.filter.ServerHttpObservationFilter检测HTTP服务器观察。
* Spring WebFlux可以使用新的org.springframework.web.filter.reactive.ServerHttpObservationFilter检测HTTP服务器观察。
* 对于Flux和Mono的Micrometer Context Propagation集成，从控制器方法返回值。


## 测试

* 支持在JVM上或GraalVM本机映像中测试AOT处理的应用程序上下文。
* 集成HtmlUnit 2.64 +请求参数处理。
* Servlet模拟（MockHttpServletRequest、MockHttpSession）现在基于Servlet API 6.0。


## 参考引用

* https://spring.io/blog/2022/11/16/spring-framework-6-0-goes-ga
* Spring 5 开发大全（北京大学出版社出版）<https://github.com/waylau/spring-5-book>
* 原文同步至<https://waylau.com/new-features-and-enhancements-in-spring-framework-6/>