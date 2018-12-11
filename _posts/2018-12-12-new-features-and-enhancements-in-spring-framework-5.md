---
layout: post
title: Spring Framework 5中的新功能和增强功能
date: 2018-12-12 00:22
author: admin
comments: true
categories: [Spring]
tags: [Spring]
---

Spring 5 是一个重要的版本，距离 Spring 4 发布有四年多了。通过本文的介绍，我们一起来快速了解 Spring 5 发行版中的那些令人兴奋的特性。

<!-- more -->

## 基准升级

要构建和运行 Spring 5 应用程序，你至少需要 Java EE 7 和 JDK 8。以前的 JDK 和 Java EE 版本不再支持。 Java EE 7 包含：

* Servlet 3.1
* JMS 2.0
* JPA 2.1
* JAX-RS 2.0
* Bean Validation 1.1

与 Java 基准类似，许多其他框架的基准也有变化。例如：

* Hibernate 5
* Jackson 2.6
* EhCache 2.10
* JUnit 5
* Tiles 3

另外，各种服务器的最低支持版本也已经升级。包括：

* Tomcat 8.5+
* Jetty 9.4+
* WildFly 10+
* Netty 4.1+
* Undertow 1.4+


同时，Spring 5 已经与最新的 Jave EE 8 API 集成，这意味着，用户可以使用 Spring 5 来创建最新的功能的应用。

## 兼容最新的 JDK 9

Spring 5 支持 JDK 9。在运行时，类路径以及模块路径与 JDK 9 完全一致。

Spring 5 使用了 Java 8 和  Java 9 版本中的许多新特性。例如：

* Spring 接口中的默认方法。
* 基于 Java 8 反射增强的内部代码改进。
* 在框架代码中使用函数式编程，如 Lambda 表达式 和 Stream 流。

同时，Spring 5 的后续版本将会积极做好 JDK 10 的适配工作。

## 响应式编程模型

响应式编程是 Spring 5 最重要的特性之一。响应式编程提供了另一种编程风格，专注于构建对事件做出响应的应用程序。Spring 5 包含响应流和 Reactor（由 Spring 团队提供的 Reactive Stream 的 Java 实现）。

在 Spring 5 中，Web 开发将会划分为两个分支，即传统的基于 Servlet 的 Web 编程（spring-webmvc 模块），以及支持响应式编程模型 Spring WebFlux (spring-web-reactive 模块)。


构建在 Reactive Streams API 上 的 Web 应用程序，可以在非阻塞服务器（例如Netty、Undertow 和 Servlet 3.1+ 容器）上运行。



## 函数式编程

除了响应式功能之外，Spring 5 还提供了一个函数式 Web 框架。它提供了使用函数式编程风格来定义端点的特性。该框架引入了两个基本组件：HandlerFunction 和 RouterFunction。

HandlerFunction 表示处理接收到的请求并生成响应的函数。 RouterFunction 替代了 @RequestMapping 注解。它用于将接收到的请求路由到处理函数。例如：

```java
import static org.springframework.http.MediaType.APPLICATION_JSON;
import static org.springframework.web.reactive.function.server.RequestPredicates.*;

PersonRepository repository = ...
PersonHandler handler = new PersonHandler(repository);

RouterFunction<ServerResponse> personRoute =
    route(GET("/person/{id}").and(accept(APPLICATION_JSON)), handler::getPerson)
        .andRoute(GET("/person").and(accept(APPLICATION_JSON)), handler::listPeople)
        .andRoute(POST("/person").and(contentType(APPLICATION_JSON)), handler::createPerson);
```

## 多语言的支持

Spring 5 支持 Apache Groovy 、Kotlin 以及其他众多的动态语言。这些动态语言包括：

* JRuby 1.5+
* Groovy 1.8+
* BeanShell 2.0

Spring 5 支持 Kotlin 1.1+ 。Kotlin 是一种静态类型的 JVM 语言，它让代码具有表现力、简洁性和可读性。同时，Kotlin 与用 Java 编写的现有库拥有良好的互操作性。

Spring 5 框架为 Kotlin 提供了一流的支持，允许开发人员编写 Kotlin 应用程序，其开发体验感觉 Spring 框架就是 Kotlin 的原生框架一样。

以下就是一个 Kotlin 编写的路由到特定端点的例子：

```kotlin
@Bean
fun apiRouter() = router {
    (accept(APPLICATION_JSON) and "/api").nest {
        "/book".nest {
            GET("/", bookHandler::findAll)
            GET("/{id}", bookHandler::findOne)
        }
        "/video".nest {
            GET("/", videoHandler::findAll)
            GET("/{genre}", videoHandler::findByGenre)
        }
    }
}
```

## 支持 HTTP/2

Spring 5 将提供专用的 HTTP/2 特性支持，以及对 JDK 9 中的新 HTTP 客户端的支持。尽管 HTTP/2 的服务器推送功能已经可以通过 Jetty Servlet 引擎的ServerPushFilter 类来实现，但是如果 Spring 5 能够提供开箱即用的 HTTP/2 性能增强功能，势必会提升用户的开发体验。

## 清理了旧代码

随着 Java、JavaEE 和其他一些框架基准版本的增加，Spring 5 取消了对以下几个框架的支持，如:

* Portlet
* Velocity
* JasperReports
* XMLBeans
* JDO
* Guava

这些被取消的框架，都可以用最新的 Java EE 标准来实现替换。

Spring 5 不再支持一些过时的 API。被剔除的是 Hibernate 3和4版本，它们已经被Hibernate 5所替换。

Spring 5 对包级别也进行了清理。Spring 5 不再支持`beans.factory.access`、`jdbc.support.nativejdbc`、`mock.staticmock` 以及 `web.view.tiles2M` 等包。

## 更强的测试套件

Spring Test 拥有了更强的测试套件，包括支持 Spring WebFlux 服务器端点集成测试的 WebTestClient。 WebTestClient 使用模拟请求和响应来避免运行服务器，并能够直接绑定到 WebFlux 服务器基础架构中。

WebTestClient 可以被绑定到一个真实的服务器或者与控制器一起工作。 

以下例子演示了 WebTestClient 绑定到 localhost 地址：

```java
WebTestClient testClient = WebTestClient
  .bindToServer()
  .baseUrl("http://localhost:8080")
  .build();
```

另外一个例子，则演示了 WebTestClient 绑定到 RouterFunction：

```java
RouterFunction bookRouter = RouterFunctions.route(
  RequestPredicates.GET("/books"),
  request -> ServerResponse.ok().build()
);

WebTestClient
  .bindToRouterFunction(bookRouter)
  .build().get().uri("/books")
  .exchange()
  .expectStatus().isOk()
  .expectBody().isEmpty();
```

## 参考引用

* 更多有关Spring的内容，可以参阅《Spring 5 开发大全》：<https://github.com/waylau/spring-5-book>
* 原文同步至<https://waylau.com/new-features-and-enhancements-in-spring-framework-5/>