---
layout: post
title: Spring Framework 4.2 中的新功能和增强功能
date: 2015-10-07 01:08
author: admin
comments: true
categories: [Spring]
tags: [Spring]
---

至今为止，Spring Framework 的最新版本为 4.2.1.RELEASE。
那么 Spring Framework 4.2 中的又有哪些新功能和增强功能呢？

<!-- more -->

## 核心容器改进

* 如 @bean 注释,就如同得到发现和处理 Java 8 默认方法一样,可以允许组合配置类与默认@bean 接口方法。
* 配置类现在可以声明 @import 作为常规组件类,允许引入的配置类和组件类进行混合。
* 配置类可以声明一个 @Order 值,用来得到相应的处理顺序(例如重写 bean 的名字)，即使通过类路径扫描检测。
* @Resource 注入点支持 @Lazy 声明,类似于 @autowired, 用于接收用于请求目标 bean 的懒初始化代理。
* 现在的应用程序事件基础架构提供了一个基于注解的模型以及发布任意事件的能力。
	* 任何受管 bean 的公共方法使用 @EventListener 注解来消费事件。
	* @TransactionalEventListener 提供事务绑定事件支持。
* Spring Framework 4.2引入了一流的支持声明和查找注释属性的别名。新 @AliasFor 注解可用于声明一双别名属性在一个注释中或从一个属性在一个声明一个别名定义注解在元注释一个属性组成。
	* 下面的注解已加了 @AliasFor 为了支持提供更有意义的 value  属性的别名: @Cacheable, @CacheEvict, @CachePut, @ComponentScan, @ComponentScan.Filter, @ImportResource, @Scope, @ManagedResource, @Header, @Payload, @SendToUser, @ActiveProfiles, @ContextConfiguration, @Sql, @TestExecutionListeners, @TestPropertySource, @Transactional, @ControllerAdvice, @CookieValue, @CrossOrigin, @MatrixVariable, @RequestHeader, @RequestMapping, @RequestParam, @RequestPart, @ResponseStatus, @SessionAttributes, @ActionMapping, @RenderMapping, @EventListener, @TransactionalEventListener
	* 例如，spring-test 的 @ContextConfiguration 现在声明如下：



		public @interface ContextConfiguration {
		
		    @AliasFor("locations")
		    String[] value() default {};
		
		    @AliasFor("value")
		    String[] locations() default {};
		
		    // ...
		}



	* 同样, 组合注解（composed annotations）从元注解覆盖的属性,现在可以使用 @AliasFor 进行细粒度控制哪些属性是覆盖在一个注释的层次结构。事实上,现在可以声明一个别名给元注释的 value 属性。
	* 例如，开发一个组合注解用于一个自定义的属性的覆盖



		@ContextConfiguration
		public @interface MyTestConfig {
		
		    @AliasFor(annotation = ContextConfiguration.class, attribute = "value")
		    String[] xmlFiles();
		
		    // ...
		}


	* 见 [Spring Annotation Programming Model](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#annotation-programming-model)
* 许多改进Spring的搜索算法用于寻找元注解。例如,局部声明组合注解现在喜欢继承注解。
* 从元注解覆盖属性的组合注解，可以被发现在接口和 abstract, bridge, & interface 方法就像在类，标准方法，构造函数，和字段。
* Map 表示的注解属性(和 AnnotationAttributes 实例)可以 synthesized (合成，即转换)成一个注解。
* 基于字段的数据绑定的特点(DirectFieldAccessor)与当前的基于属性的数据绑定关联(BeanWrapper)。特别是,基于字段的绑定现在支持集合,数组和 Map 的导航。
* DefaultConversionService 现在提供开箱即用的转化器给 Stream, Charset, Currency, 和 TimeZone. 这些转换器可以独立的添加到任何 ConversionService 
* DefaultFormattingConversionService 提供开箱即用的支持 JSR-354 的 Money & Currency 类型 (前提是 'javax.money' API 出现在 classpath): 这些被命名为 MonetaryAmount 和 CurrencyUnit。支持使用 @NumberFormat
*  @NumberFormat 现在作为元注解使用
*  JavaMailSenderImpl 中新的 testConnection() 方法用于检查与服务器的连接
*  ScheduledTaskRegistrar 用于暴露调度的任务
*  Apache commons-pool2 现在支持用于 AOP CommonsPool2TargetSource 的池化
*  引入 StandardScriptFactory 作为脚本化 bean 的 JSR-223 的基本机制，通过 XML 中的 `lang:std` 元素暴露。支持如 JavaScript 和 JRuby。（注意：JRubyScriptFactory 和 `lang:jruby`现在不推荐使用了 , 推荐用 JSR-223）
    
## 数据访问改进

* javax.transaction.Transactional 现在可以通过 AspectJ 支持
* SimpleJdbcCallOperations 现在支持命名绑定
* 完全支持 Hibernate ORM 5.0: 作为 JPA 供应商 (自动适配)和原生的 API 一样 (在新的 org.springframework.orm.hibernate5 包中涵盖了该内容)
* 嵌入式数据库可以自动关联唯一名字，并且 `<jdbc:embedded-database>` 支持新的 database-name 属性。见下面“测试改进”内容

## JMS 改进

* autoStartup 属性可以通过 JmsListenerContainerFactory 进行控制
* 应答类型 Destination 可以配置在每个监听器容器
* @SendTo 的值可以用 SpEL 表达式
* 响应目的地可以通过 JmsResponse 在[运行时计算](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#jms-annotated-response)
* @JmsListener 是可以可以重复的注解用于声明多个 JMS 容器在相同的方法上 (若你还没有用上 Java8 请使用新引入的 @JmsListeners)。

## Web 改进

* 支持 HTTP Streaming 和 Server-Sent Events , se见 “[HTTP Streaming](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#mvc-ann-async-http-streaming)”
* 内建支持 CORS ，包括全局 (MVC Java 配置和 XML 命名空间) 和本地 (如 @CrossOrigin) 配置。见 26 章, [CORS 支持](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#cors)
* HTTP 缓存升级
	* 新的 CacheControl 构建器; 插入 ResponseEntity, WebContentGenerator, ResourceHttpRequestHandler
	* 改进的 ETag/Last-Modified 在 WebRequest 中支持
* 自定义映射注解使用 @RequestMapping 作为 元数据注解
* AbstractHandlerMethodMapping  中的 public 方法用于运行时注册和注销请求映射
* AbstractDispatcherServletInitializer 中的 Protected createDispatcherServlet 方法用来进一步自定义 DispatcherServlet 实例
* HandlerMethod 作为 @ExceptionHandler方法的方法参数,特别是方便 @ControllerAdvice 组件
* java.util.concurrent.CompletableFuture 作为 @Controller 方法返回值类型
* 字节范围（Byte-range）的请求支持在 HttpHeaders，用于静态资源
* @ResponseStatus 发现嵌套异常。
* 在 RestTemplate 中的 UriTemplateHandler 扩展端点
	* DefaultUriTemplateHandler 暴露 baseUrl 属性和路径段的编码选项
	* 扩展端点可以使用插入任何 URI 模板库
* [OkHTTP](http://square.github.io/okhttp/) 与 RestTemplate 集成
* 自定义 baseUrl 在 MvcUriComponentsBuilder 选择方法。
* 序列化/反序列化异常消息现在记录为 WARN 级别
* 默认的 JSON 前缀改变了从`{}&&`改为更安全的`)]}’,`
* 新的 RequestBodyAdvice 扩展点和内置的实现支持 Jackson 的  在 @RequestBody 的 @JsonView
* 当使用 GSON 或 Jackson 2.6 +,处理程序方法的返回类型是用于提高参数化类型的序列化，比如 `List<Foo>`
* 引入的 ScriptTemplateView 作为 JSR-223 的脚本化 web 视图机制为基础,关注 JavaScript 视图模板 Nashorn (JDK 8)。

## WebSocket 消息改进

* 暴露展示信息关于用户的连接和订阅:
	* 新 SimpUserRegistry 公开为一个名为“userRegistry”的bean。
	* 共享在服务器集群的展示信息(见代理中继配置选项)
* 解决用户目的地在集群的服务器(见代理中继配置选项)。
* StompSubProtocolErrorHandler 扩展端点用来自定义和控制 STOMP ERROR 帧给用户
* 全局 @MessageExceptionHandler 方法通过 @ControllerAdvice 组件
* 心跳和 SpEL 表达式'selector'头用 SimpleBrokerMessageHandler 订阅
* STOMP 客户端使用TCP 和 WebSocket; 见 25.4.13, “[STOMP 客户端](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#websocket-stomp-client)”
* @SendTo 和 @SendToUser 可以包含目标变量的占位符。
Jackson 的 @JsonView 支持 @MessageMapping 和 @SubscribeMapping 方法返回值
* ListenableFuture 和 CompletableFuture 是从 @MessageMapping 和 @SubscribeMapping 方法返回类型值
* MarshallingMessageConverter 用于 XML 有效载荷

## 测试改进

* 基于 JUnit 集成测试现在可以执行 JUnit 规则而不是SpringJUnit4ClassRunner。这允许基于 spring 的集成测试与运行JUnit 的 Parameterized 或第三方 运行器 MockitoJUnitRunner 等。详见 [Spring JUnit 规则](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#testcontext-junit4-rules)
* Spring MVC Test 框架，现在支持第一类 HtmlUnit，包括集成  Selenium’s WebDriver,允许基于页面的 Web 应用测试而无需部署到 Servlet 容器。详见  [14.6.2, “HtmlUnit 集成”](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#spring-mvc-test-server-htmlunit)
* AopTestUtils  是一个新的测试工具，允许开发者获得潜在的目标对象的引用隐藏在一个或多个 Spring 代理。详见 [13.2.1, “常见测试工具”](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#unit-testing-utilities)
* ReflectionTestUtils 现在支持 setting 和 getting static 字段,包括常量
* bean 定义归档文件的原始顺序，通过 @ActiveProfiles 声明，现在保留为了支持用例,如 Spring 的 ConfigFileApplicationListener 引导加载配置文件基于活动归档文件的名称。
* @DirtiesContext 支持新 BEFORE_METHOD BEFORE_CLASS,BEFORE_EACH_TEST_METHOD 模式，用于测试之前关闭ApplicationContext——例如,如果一些烦人的(即，有待确定)测试在一个大型测试套件的 ApplicationContext 的原始配置已经损坏。
* @Commit 是新的注解直接可以用来代替 @Rollback(false)
* @Rollback 用来配置类级别的默认回滚语义
	* 因此,现在 @TransactionConfiguration 弃用,在后续版本将被删除。
* @Sql 现在支持内联 SQL 语句的执行通过一个新的 statements 属性
* ContextCache 用于缓存测试之间的 ApplicationContext，而现在这是一个公开的 API ，默认的实现可以替代自定义的缓存需求
* DefaultTestContext, DefaultBootstrapContext, 和 DefaultCacheAwareContextLoaderDelegate 现在是公开的类，支持子包，允许自定义扩展
* TestContextBootstrapper 现在负责构建 TestContext
* 在 Spring MVC Test 框架，MvcResult  详情可以被日志记录在 DEBUG 级别或者写入自定义的 OutputStream 或 Writer。详见 log(), print(OutputStream), 和 MockMvcResultHandlers 的 print(Writer) 方法
* JDBC XML名称空间支持一个新的 `<jdbc:embedded-database>` 的 database-name 属性,允许开发人员为嵌入式数据库设置独特的名字——例如,通过一个 SpEL 表达式或 前活动bean定义配置文件所影响的占位符属性
* 嵌入式数据库现在可以自动分配一个唯一的名称,允许常用的测试数据库配置在不同的 ApplicationContext 的测试套件中。
参见[18.8.6“给嵌入式数据库生成惟一名称”](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#jdbc-embedded-database-unique-names)的细节。

## 参考

* [《Spring Framework 4.x参考文档》中文翻译](https://github.com/waylau/spring-framework-4-reference)
* <http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/>