---
layout: post
title: Spring Framework HTTP服务客户端详解
date: 2026-06-28 00:22
author: admin
comments: true
categories: [Spring]
tags: [Spring]
---


Spring Framework HTTP 服务客户端（HTTP Service Client/HTTP Interface）最初引入自Spring Framework 6.0。基础声明式HTTP接口能力，同时支持RestClient/WebClient/RestTemplate三种底层客户端，从Spring Framework 7.0开始，支持自动Bean注册、服务分组管理。

本文详细介绍Spring Framework HTTP服务客户端用法。

<!-- more -->



## 1. 定义接口


可以定义带有 `@HttpExchange` 注解方法的 Java 接口，通过 `HttpServiceProxyFactory` 生成动态代理客户端，底层可适配RestClient/WebClient/RestTemplate三种客户端。

服务端 Controller 也可实现同一套接口，统一接口定义，前后端对齐。


```java
public interface RepositoryService {
    @GetExchange("/repos/{owner}/{repo}")
    Repository getRepository(@PathVariable String owner, @PathVariable String repo);
}
```

### 接口级别统一配置注解

```java
@HttpExchange(url = "/repos/{owner}/{repo}", accept = "application/vnd.waylau.v3+json")
public interface RepositoryService {
    @GetExchange
    Repository getRepository(@PathVariable String owner, @PathVariable String repo);

    @PatchExchange(contentType = MediaType.APPLICATION_FORM_URLENCODED_VALUE)
    void updateRepository(
            @PathVariable String owner,
            @PathVariable String repo,
            @RequestParam String name,
            @RequestParam String description,
            @RequestParam String homepage
    );
}
```

## 2. 创建代理工厂

### 基于 RestClient
```java
RestClient restClient = RestClient.create("https://api.waylau.com");
RestClientAdapter adapter = RestClientAdapter.create(restClient);
HttpServiceProxyFactory factory = HttpServiceProxyFactory.builderFor(adapter).build();
```

### 基于 WebClient
```java
WebClient webClient = WebClient.create("https://api.waylau.com");
WebClientAdapter adapter = WebClientAdapter.create(webClient);
HttpServiceProxyFactory factory = HttpServiceProxyFactory.builderFor(adapter).build();
```

### 基于 RestTemplate
```java
RestTemplate restTemplate = new RestTemplate();
RestTemplateAdapter adapter = RestTemplateAdapter.create(restTemplate);
HttpServiceProxyFactory factory = HttpServiceProxyFactory.builderFor(adapter).build();
```

## 3. 获取代理客户端并调用

```java
RepositoryService service = factory.createClient(RepositoryService.class);
Repository repo = service.getRepository("spring-projects", "spring-framework");
```

## 4. 方法参数注解支持

| 参数类型/注解 | 说明 |
|--------------|------|
| URI | 动态覆盖注解上定义的请求地址 |
| UriBuilderFactory | 自定义 URI 模板解析工厂，覆盖客户端默认配置 |
| HttpMethod | 动态修改请求方法，覆盖注解定义 |
| @RequestHeader | 添加请求头，支持单值、集合、Map、多值Map |
| @PathVariable | 路径占位变量，支持单个值或 Map 批量传入 |
| @RequestAttribute | 请求属性，仅 RestClient/WebClient 支持 |
| @RequestBody | 请求体，普通实体或响应式 Publisher(Mono/Flux) |
| @RequestParam | URL 查询参数；表单编码时放入请求体 |
| @RequestPart | 多部件上传：普通字段、文件Resource、JSON实体、自定义头部件 |
| MultipartFile | Spring MVC 文件上传对象 |
| @CookieValue | 请求 Cookie，支持单值、Map、集合 |

> 参数默认不允许 null，除非注解设置 `required=false` 或参数标记为可选。

## 5. 自定义参数解析器

可实现 `HttpServiceArgumentResolver` 自定义参数转换逻辑，例如自定义 Search 对象自动拆解为查询参数：

### 自定义解析器
```java
static class SearchQueryArgumentResolver implements HttpServiceArgumentResolver {
    @Override
    public boolean resolve(Object argument, MethodParameter parameter, HttpRequestValues.Builder requestValues) {
        if (parameter.getParameterType().equals(Search.class)) {
            Search search = (Search) argument;
            requestValues.addRequestParameter("owner", search.owner());
            requestValues.addRequestParameter("language", search.language());
            requestValues.addRequestParameter("query", search.query());
            return true;
        }
        return false;
    }
}
```

### 注册解析器并使用

```java
RestClient restClient = RestClient.builder().baseUrl("https://api.waylau.com/").build();
RestClientAdapter adapter = RestClientAdapter.create(restClient);
HttpServiceProxyFactory factory = HttpServiceProxyFactory
        .builderFor(adapter)
        .customArgumentResolver(new SearchQueryArgumentResolver())
        .build();
RepositoryService repositoryService = factory.createClient(RepositoryService.class);

Search search = Search.create()
        .owner("spring-projects")
        .language("java")
        .query("rest")
        .build();
List<Repository> repositories = repositoryService.searchRepository(search);
```

## 6. 返回值类型

### 同步客户端（RestClient / RestTemplate）支持返回值

| 返回类型 | 说明 |
|----------|------|
| void | 仅发起请求，忽略响应 |
| HttpHeaders | 返回响应头，丢弃响应体 |
| T | 直接返回解析后的实体对象 |
| ResponseEntity<Void> | 仅状态码+响应头，无响应体 |
| ResponseEntity<T> | 完整响应：状态、头、解析后实体 |

### 响应式客户端（WebClient）额外支持响应式类型

| 返回类型 | 说明 |
|----------|------|
| Mono<Void> | 异步请求，丢弃响应体 |
| Mono<HttpHeaders> | 异步获取响应头 |
| Mono<T> | 异步返回单个实体 |
| Flux<T> | 流式返回多个对象 |
| Mono<ResponseEntity<Flux<T>>> | 流式响应完整包装 |

RestClientAdapter 额外支持 `InputStream` / `ResponseEntity<InputStream>`，可读取原始响应流。

## 7. 异常处理
代理客户端默认 4xx/5xx 都会抛出异常，可在底层客户端统一配置全局状态处理器：
```java
// RestClient 全局异常处理
RestClient restClient = RestClient.builder()
        .defaultStatusHandler(HttpStatusCode::isError, (request, response) -> {
            // 统一错误逻辑
        })
        .build();
RestClientAdapter adapter = RestClientAdapter.create(restClient);
HttpServiceProxyFactory factory = HttpServiceProxyFactory.builderFor(adapter).build();
```

## 8. 适配器装饰器

可通过 `exchangeAdapterDecorator` 包装适配器，统一拦截所有代理请求，例如内置装饰器实现「404 不抛异常，返回空实体」：
```java
HttpServiceProxyFactory factory = HttpServiceProxyFactory.builderFor(restClientAdapter)
        .exchangeAdapterDecorator(NotFoundRestClientAdapterDecorator::new)
        .build();
```

## 9. HTTP 服务分组

大量接口代理时，每个接口单独配置 Bean 会产生大量重复代码。Spring 提供「服务分组」能力，同一分组下的接口共用一套客户端与代理工厂配置，自动注册为 Spring Bean。

### 使用注解声明分组
```java
@Configuration
// 分组echo：手动指定接口类
@ImportHttpServices(group = "echo", types = {EchoServiceA.class, EchoServiceB.class})
// 分组greeting：扫描包下所有接口
@ImportHttpServices(group = "greeting", basePackageClasses = GreetServiceA.class)
public class ClientConfig {
}
```

### 编程式注册分组
```java
public class MyHttpServiceRegistrar extends AbstractHttpServiceRegistrar {
    @Override
    protected void registerHttpServices(GroupRegistry registry, AnnotationMetadata metadata) {
        registry.forGroup("echo").register(EchoServiceA.class, EchoServiceB.class);
        registry.forGroup("greeting").detectInBasePackages(GreetServiceA.class);
    }
}

@Configuration
@Import(MyHttpServiceRegistrar.class)
public class ClientConfig {
}
```

### 统一配置分组客户端
通过 `RestClientHttpServiceGroupConfigurer` 批量修改分组配置：
```java
@Bean
public RestClientHttpServiceGroupConfigurer groupConfigurer() {
    return groups -> {
        // 单独配置echo分组
        groups.filterByName("echo").forEachClient((group, clientBuilder) -> {
            clientBuilder.defaultHeader("group", "echo");
        });
        // 所有分组统一配置
        groups.forEachClient((group, clientBuilder) -> {
            clientBuilder.requestInterceptor(logInterceptor);
        });
    };
}
```

### 注入使用
```java
@RestController
public class EchoController {
    private final EchoService echoService;
    // 直接自动注入
    public EchoController(EchoService echoService) {
        this.echoService = echoService;
    }
}
```

### 多分组同接口场景
同一接口存在多个分组时，无法直接按类型注入，可通过 `HttpServiceProxyRegistry` 根据分组名获取：
```java
@RestController
public class EchoController {
    private final EchoService echo1;
    private final EchoService echo2;

    public EchoController(HttpServiceProxyRegistry registry) {
        this.echo1 = registry.getClient("echo1", EchoService.class);
        this.echo2 = registry.getClient("echo2", EchoService.class);
    }
}
```

Spring Boot、Spring Security、Spring Cloud 均基于该分组机制实现自动配置、负载均衡、OAuth 认证等能力。


## 参考引用

以下是Spring生态开发常用教程。


* [《Spring Boot 企业级应用开发实战》](https://github.com/waylau/spring-boot-enterprise-application-development)（北京大学出版社)
* [《Spring Cloud 微服务架构开发实战》](https://github.com/waylau/spring-cloud-microservices-development)（北京大学出版社)
* [《Spring 5 开发大全》](https://github.com/waylau/spring-5-book/)（北京大学出版社）
* [《大型互联网应用轻量级架构实战》](https://github.com/waylau/lite-book-demos/)（北京大学出版社）
* 《跟老卫学Spring Cloud Stream开发》开源免费教程， <https://github.com/waylau/spring-cloud-stream-tutorial/>
* [《Vue.js＋Spring Boot全栈开发实战》](https://github.com/waylau/full-stack-development-with-vuejs-and-spring-boot)（人民邮电出版社）
* Java+AI全栈工程师， <https://class.imooc.com/sale/javaaifullstack>


