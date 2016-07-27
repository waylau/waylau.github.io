---
layout: post
title: 用 Jersey 2 和 Spring 4 构建 RESTful web service
date: 2014-11-22 02:41
author: admin
comments: true
categories: [Jersey]
tags: [Jersey,Spring,REST]
---

本文介绍了如何通过 Jersey 框架优美的在 Java 实现了 REST 的 API。CRUD 的 操作存储在 MySQL 中

<!-- more -->

##1. 示例

###1.1 为什么

Spring 可以对于 REST 有自己的实现(见 [https://spring.io/guides/tutorials/rest/](https://spring.io/guides/tutorials/rest/))。 但本文展示的是用 “官方” 的 方法来实现 REST ，即使用 Jersey。

###1.2 它是做什么的?

管理 资源。 REST API 将允许创建、检索、更新和删除这样的资源。

###1.3 架构及技术

![](http://99btgc01.info/uploads/2014/11/Rest-Demo-Diagram.png)

本示例项目使用多层结构，基于“Law of Demeter (LoD) or principle of least knowledge”（迪米特法则），是说一个软件实体要尽可能的只与和它最近的实体进行通讯。通常被表述为：talk only to your immediate friends ( 只和离你最近的朋友进行交互)。  
 “talk”，其实就是对象间方法的调用。这条规则表明了对象间方法调用的原则：（1）调用对象本身的方法；（2）调用通过参数传入的对象的方法；（3）在方法中创建的对象的方法；（4）所包含对象的方法。

主要分为三层：

* 第一层：Jersey 实现对 REST 的支持，拥有 [外观模式](http://en.wikipedia.org/wiki/Facade_pattern)的角色并代理到逻辑业务层
* 业务层: 发生逻辑的地方
* 数据访问层： 是与持久数据存储（在我们的例子中是 MySql数据库)交互的地方



简述下技术框架：

####1.3.1. Jersey (外观)

[Jersey](https://jersey.java.net/) 是开源、拥有产品级别的质量，提供构建  RESTful Web Services,支持  JAX-RS APIs ，提供 [JAX-RS](https://jax-rs-spec.java.net/) (JSR 311 & JSR 339) 参考实现。

####1.3.2. Spring (业务层)

在我看来没有什么 比 [Spring](http://projects.spring.io/spring-framework/) 更好的办法让 pojo 具有不同的功能。 你会发现在本教程用 Jersey 2 和 Spring 4 构建 RESTful web service

####1.3.3. JPA 2 / Hibernate (持久层)

使用 Hibernate 实现 DAO 模式。

####1.3.4. Web 容器

用 Maven 打包成 .war 文件开源部署在任意容器。一般用 [Tomcat](http://tomcat.apache.org/) 和 [Jetty](http://www.eclipse.org/jetty/) ，也可以是 Glassfih, Weblogic, JBoss 或 WebSphere.

####1.3.5. MySQL 数据库

示例数据存储在一个 MySQL 表:

![](http://99btgc01.info/uploads/2014/11/database-schema.png)

####1.3.6. 技术版本

Jersey 2.9

Spring 4.0.3

Hibernate 4

Maven 3

Tomcat 7

Jetty 9

MySql 5.6

###1.4. 源码

见[https://github.com/waylau/RestDemo/tree/master/jersey-2-spring-4-rest](https://github.com/waylau/RestDemo/tree/master/jersey-2-spring-4-rest)

##2. 配置

开始呈现 REST API 的设计和实现之前,我们需要做一些配置。

###2.1. 项目依赖

[Jersey Spring 扩展包](https://github.com/waylau/Jersey-2.x-User-Guide/tree/master/Chapter%2022.%20Spring%20DI%20%E4%BD%BF%E7%94%A8%20Spring%20%E6%B3%A8%E5%85%A5) 是必须要放在 项目 classpath 中。在 pom.xml 中添加下面依赖：

	<dependency>
	    <groupId>org.glassfish.jersey.ext</groupId>
	    <artifactId>jersey-spring3</artifactId>
	    <version>${jersey.version}</version>
	    <exclusions>
	        <exclusion>
	            <groupId>org.springframework</groupId>
	            <artifactId>spring-core</artifactId>
	        </exclusion>            
	        <exclusion>
	            <groupId>org.springframework</groupId>
	            <artifactId>spring-web</artifactId>
	        </exclusion>
	        <exclusion>
	            <groupId>org.springframework</groupId>
	            <artifactId>spring-beans</artifactId>
	        </exclusion>
	    </exclusions>            
	</dependency>
	<dependency>
	    <groupId>org.glassfish.jersey.media</groupId>
	    <artifactId>jersey-media-json-jackson</artifactId>
	    <version>2.4.1</version>
	</dependency>

*注意: jersey-spring3.jar 使用的是他自己的 Spring 库版本，所以如果你想使用自己的 (本例是使用 Spring 4.0.3.Release),你需要将这些库手动的移除。如果想看到其他 的库的依赖，请查看项目源码中的 pom.xml*

###2.2. web.xml

应用部署描述

	<?xml version="1.0" encoding="UTF-8"?>
	<web-app version="3.0" xmlns="http://java.sun.com/xml/ns/javaee"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd">
	    <display-name>Demo - Restful Web Application</display-name>
	 
	    <listener>
	        <listener-class>
	            org.springframework.web.context.ContextLoaderListener
	        </listener-class>
	    </listener>
	 
	    <context-param>
	        <param-name>contextConfigLocation</param-name>
	        <param-value>classpath:spring/applicationContext.xml</param-value>
	    </context-param>
	 
	    <servlet>
	        <servlet-name>jersey-serlvet</servlet-name>
	        <servlet-class>
	            org.glassfish.jersey.servlet.ServletContainer
	        </servlet-class>
	        <init-param>
	            <param-name>javax.ws.rs.Application</param-name>
	            <param-value>org.codingpedia.demo.rest.RestDemoJaxRsApplication</param-value>            
	        </init-param>        
	        <load-on-startup>1</load-on-startup>
	    </servlet>
	 
	    <servlet-mapping>
	        <servlet-name>jersey-serlvet</servlet-name>
	        <url-pattern>/*</url-pattern>
	    </servlet-mapping>
	 
	    <resource-ref>
	        <description>Database resource rest demo web application </description>
	        <res-ref-name>jdbc/restDemoDB</res-ref-name>
	        <res-type>javax.sql.DataSource</res-type>
	        <res-auth>Container</res-auth>
	    </resource-ref>    
	</web-app>

####2.2.1. Jersey-servlet

注意 Jersey servlet 的配置，`javax.ws.rs.core.Application` 类定义了 JAX-RS 应用组件(root 资源 和 提供者 类) .本例使用 `ResourceConfig`, 是 Jersey 自己实现的 `Application` 类，提供了简化  JAX-RS 组件的能力。详见[JAX-RS 应用模型](https://github.com/waylau/Jersey-2.x-User-Guide/blob/master/Chapter%204.%20Application%20Deployment%20and%20Runtime%20Environments%20%E5%BA%94%E7%94%A8%E9%83%A8%E7%BD%B2%E5%92%8C%E8%BF%90%E8%A1%8C%E6%97%B6%E7%8E%AF%E5%A2%83/4.2.%20JAX-RS%20Application%20Model%20%E5%BA%94%E7%94%A8%E6%A8%A1%E5%9E%8B.md)

`org.codingpedia.demo.rest.RestDemoJaxRsApplication` 是自己实现的 `ResourceConfig`类，注册应用的  resources, filters, exception mappers 和 feature :
	
	package org.codingpedia.demo.rest.service;
	 
	//imports omitted for brevity 
	 
	/**
	 * Registers the components to be used by the JAX-RS application
	 * 
	 * @author ama
	 * 
	 */
	public class RestDemoJaxRsApplication extends ResourceConfig {
	 
	    /**
	     * Register JAX-RS application components.
	     */
	    public RestDemoJaxRsApplication() {
	        // register application resources
	        register(PodcastResource.class);
	        register(PodcastLegacyResource.class);
	 
	        // register filters
	        register(RequestContextFilter.class);
	        register(LoggingResponseFilter.class);
	        register(CORSResponseFilter.class);
	 
	        // register exception mappers
	        register(GenericExceptionMapper.class);
	        register(AppExceptionMapper.class);
	        register(NotFoundExceptionMapper.class);
	 
	        // register features
	        register(JacksonFeature.class);
	        register(MultiPartFeature.class);
	    }
	}

注意：

* `org.glassfish.jersey.server.spring.scope.RequestContextFilter` 是 Spring filter 提供了 JAX-RS 和 Spring 请求属性之间的桥梁。
* `org.codingpedia.demo.rest.resource.PodcastsResource` 这是“外观”组件，通过注解 暴露了 REST 的API。稍后会描述 
* `org.glassfish.jersey.jackson.JacksonFeature`,是一个  feature ，用 Jackson JSON 的提供者来解释 JSON。 

####2.1.2. Spring 配置

配置文件在 classpath 目录下的 spring/applicationContext.xml:

	<beans xmlns="http://www.springframework.org/schema/beans"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	    xmlns:context="http://www.springframework.org/schema/context"
	    xmlns:tx="http://www.springframework.org/schema/tx"    
	    xsi:schemaLocation="
	        http://www.springframework.org/schema/beans    
	        http://www.springframework.org/schema/beans/spring-beans.xsd
	 
	        http://www.springframework.org/schema/tx 
	        http://www.springframework.org/schema/tx/spring-tx.xsd
	 
	        http://www.springframework.org/schema/context
	        http://www.springframework.org/schema/context/spring-context.xsd">
	 
	    <context:component-scan base-package="org.codingpedia.demo.rest.*" />
	 
	    <!-- ************ JPA configuration *********** -->
	    <tx:annotation-driven transaction-manager="transactionManager" />  
	    <bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
	        <property name="entityManagerFactory" ref="entityManagerFactory" />
	    </bean>
	    <bean id="transactionManagerLegacy" class="org.springframework.orm.jpa.JpaTransactionManager">
	        <property name="entityManagerFactory" ref="entityManagerFactoryLegacy" />
	    </bean>    
	    <bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
	        <property name="persistenceXmlLocation" value="classpath:config/persistence-demo.xml" />
	        <property name="persistenceUnitName" value="demoRestPersistence" />        
	        <property name="dataSource" ref="restDemoDS" />
	        <property name="packagesToScan" value="org.codingpedia.demo.*" />
	        <property name="jpaVendorAdapter">
	            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter">
	                <property name="showSql" value="true" />
	                <property name="databasePlatform" value="org.hibernate.dialect.MySQLDialect" />
	            </bean>
	        </property>
	    </bean>     
	    <bean id="entityManagerFactoryLegacy" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
	        <property name="persistenceXmlLocation" value="classpath:config/persistence-demo.xml" />
	        <property name="persistenceUnitName" value="demoRestPersistenceLegacy" />
	        <property name="dataSource" ref="restDemoLegacyDS" />
	        <property name="packagesToScan" value="org.codingpedia.demo.*" />
	        <property name="jpaVendorAdapter">
	            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter">
	                <property name="showSql" value="true" />
	                <property name="databasePlatform" value="org.hibernate.dialect.MySQLDialect" />
	            </bean>
	        </property>
	    </bean>        
	 
	    <bean id="podcastDao" class="org.codingpedia.demo.rest.dao.PodcastDaoJPA2Impl"/>    
	    <bean id="podcastService" class="org.codingpedia.demo.rest.service.PodcastServiceDbAccessImpl" />    
	    <bean id="podcastsResource" class="org.codingpedia.demo.rest.resource.PodcastsResource" />
	    <bean id="podcastLegacyResource" class="org.codingpedia.demo.rest.resource.PodcastLegacyResource" />
	 
	    <bean id="restDemoDS" class="org.springframework.jndi.JndiObjectFactoryBean" scope="singleton">
	        <property name="jndiName" value="java:comp/env/jdbc/restDemoDB" />
	        <property name="resourceRef" value="true" />        
	    </bean>
	    <bean id="restDemoLegacyDS" class="org.springframework.jndi.JndiObjectFactoryBean" scope="singleton">
	        <property name="jndiName" value="java:comp/env/jdbc/restDemoLegacyDB" />
	        <property name="resourceRef" value="true" />        
	    </bean>    
	</beans>

其中 `podcastsResource`是指向 REST API 实体

##3. REST API (设计与实现)

###3.1. 资源

####3.1.1. 设计

REST 中的资源主要包括下面两大思想：

* 每个都指向了全球标示符（如，HTTP 中的 [URI](http://en.wikipedia.org/wiki/Uniform_resource_identifier)）
* 有一个或多个表示（我们将在本示例使用 JSON 格式）

REST 中的资源 一般是名词 (podcasts, customers, user, accounts 等) 而不是名词 (getPodcast, deleteUser 等）

本教程使用的端点有：

* `/podcasts` – （注意复数）URI标识的资源 podcasts 集合的播客
* `/podcasts/{id}` – 通过 podcasts 的ID, URI 标识一个podcasts 资源，

####3.1.2. 实现

为求精简 ， podcast 只包含下列属性:

* id – podcast 的唯一标识
* feed –  podcast 的 feed url
* title – 标题
* linkOnPodcastpedia – 链接
* description – 描述

我用了两种 Java 类来表示 podcast 代码，是为了避免 类及其属性/方法 被 JPA 和 XML/JAXB/JSON 的注释堆满了：

* PodcastEntity.java – JPA 注解类用在 DB 和业务层
* Podcast.java – JAXB/JSON 注解类用在外观和业务层

Podcast.java
 
	package org.codingpedia.demo.rest.resource;
	 
	//imports omitted for brevity
	 
	/**
	 * Podcast resource placeholder for json/xml representation 
	 * 
	 * @author ama
	 *
	 */
	@SuppressWarnings("restriction")
	@XmlRootElement
	@XmlAccessorType(XmlAccessType.FIELD)
	public class Podcast implements Serializable {
	 
	    private static final long serialVersionUID = -8039686696076337053L;
	 
	    /** id of the podcast */
	    @XmlElement(name = "id")    
	    private Long id;
	    
	    /** title of the podcast */
	    @XmlElement(name = "title")    
	    private String title;
	        
	    /** link of the podcast on Podcastpedia.org */
	    @XmlElement(name = "linkOnPodcastpedia")    
	    private String linkOnPodcastpedia;
	    
	    /** url of the feed */
	    @XmlElement(name = "feed")    
	    private String feed;
	    
	    /** description of the podcast */
	    @XmlElement(name = "description")
	    private String description; 
	        
	    /** insertion date in the database */
	    @XmlElement(name = "insertionDate")
	    @XmlJavaTypeAdapter(DateISO8601Adapter.class)    
	    @PodcastDetailedView
	    private Date insertionDate;
	 
	    public Podcast(PodcastEntity podcastEntity){
	        try {
	            BeanUtils.copyProperties(this, podcastEntity);
	        } catch (IllegalAccessException e) {
	            // TODO Auto-generated catch block
	            e.printStackTrace();
	        } catch (InvocationTargetException e) {
	            // TODO Auto-generated catch block
	            e.printStackTrace();
	        }
	    }
	    
	    public Podcast(String title, String linkOnPodcastpedia, String feed,
	            String description) {
	        
	        this.title = title;
	        this.linkOnPodcastpedia = linkOnPodcastpedia;
	        this.feed = feed;
	        this.description = description;
	        
	    }
	    
	    public Podcast(){}
	 
	//getters and setters now shown for brevity
	}

转化成 JSON 输出如下
	
	{
	    "id":1,
	    "title":"Quarks & Co - zum Mitnehmen-modified",
	    "linkOnPodcastpedia":"http://www.podcastpedia.org/podcasts/1/Quarks-Co-zum-Mitnehmen",
	    "feed":"http://podcast.wdr.de/quarks.xml",
	    "description":"Quarks & Co: Das Wissenschaftsmagazin",
	    "insertionDate":"2014-05-30T10:26:12.00+0200"
	}

###3.2. 方法

简单的说明

* 创建 = POST
* 读 = GET
* 更新 = PUT
* 删除 = DELETE

但不是一一映射，因为 PUT 也可以创建， POST也可以用在更新。 

*注：读取和删除它是很清楚的，他们确实是和 GET 、DELETE一对一的映射。无论如何，REST 是一种架构风格，不是一个规范，你应该适应你的架构需要，但如果你想让你的 API 让更多的公众愿意使用它，你应该遵循一定的“最佳实践”。*

`PodcastRestResource` 类 是处理所有的请求

	package org.codingpedia.demo.rest.resource;
	//imports
	......................
	@Component
	@Path("/podcasts")
	public class PodcastResource {
	    @Autowired
	    private PodcastService podcastService;
	    .....................
	}

注意 类定义前面的 `@Path("/podcasts")`，所有与 podcast 关联的资源都会出现在 这个路径下。 @Path 注解值是关联 URI 的路径。

在上面的例子中，该 Java 类将托管在` /podcasts` URI 路径。`PodcastService` 接口公开的业务逻辑 到 REST 外观层。

####3.2.1. 创建 podcast
####3.2.1.1. 设计

常见的的方式利用 POST 创建资源，如前所述，创建一个新的资源，可以用 POST 和 PUT 的方法，我是这样做的：

<table border="1" cellspacing="0" cellpadding="5">
<tbody>
<tr valign="TOP">
<td bgcolor="#808080" width="25%"><b>&nbsp; Description</b></td>
<td bgcolor="#808080" width="25%"><b>&nbsp; URI</b></td>
<td bgcolor="#808080" width="25%"><b>&nbsp; HTTP method<br>
</b></td>
<td bgcolor="#808080" width="25%"><b>&nbsp; HTTP Status response</b></td>
<iframe id="tmp_downloadhelper_iframe" style="display: none;"></iframe></tr>
<tr>
<td width="25%" height="19">&nbsp;增加新的 podcast</td>
<td width="25%">&nbsp;/podcasts/</td>
<td width="25%">
<p align="CENTER">POST</p>
</td>
<td width="25%">
<p align="CENTER">201 Created</p>
</td>
</tr>
<tr>
<td width="25%" height="19">&nbsp;增加新的 podcast (必须传所有的值)</td>
<td width="25%">&nbsp;/podcasts/{id}</td>
<td width="25%">
<p align="CENTER">PUT</p>
</td>
<td width="25%">
<p align="CENTER">201 Created</p>
</td>
</tr>
</tbody>
</table>


PUT  POST 最大的区别是 ，PUT 就是把你应该事先知道资源将被创建的位置和发送所有可能值的实体。

#####3.2.1.2. 实现
######3.2.1.2.1. POST 创建一个单资源

	/**
	 * Adds a new resource (podcast) from the given json format (at least title
	 * and feed elements are required at the DB level)
	 * 
	 * @param podcast
	 * @return
	 * @throws AppException
	 */
	@POST
	@Consumes({ MediaType.APPLICATION_JSON })
	@Produces({ MediaType.TEXT_HTML })
	public Response createPodcast(Podcast podcast) throws AppException {
	    Long createPodcastId = podcastService.createPodcast(podcast);
	    return Response.status(Response.Status.CREATED)// 201
	            .entity("A new podcast has been created")
	            .header("Location",
	                    "http://localhost:8888/demo-rest-jersey-spring/podcasts/"
	                            + String.valueOf(createPodcastId)).build();
	}

注解

* @POST – 指示方法响应到 HTTP POST 请求
* @Consumes({MediaType.APPLICATION_JSON}) – 定义方法可以接受的媒体类型，本例为"application/json"
* @Produces({MediaType.TEXT_HTML}) – 定义方法产生的媒体类型本例为 "text/html"

响应

* 成功: HTTP 状态 为 201 的 text/html 文件和头的位置指定的资源已被创建
* 错误:
	* 400：没有足够的数据提供
	* 409：冲突了。如果在服务器端被确定 具有相同的 podcast 的存在

######3.2.1.2.2. 通过 PUT 创建单资源 (“podcast”) 

这将执行 更新 Podcast 处理。

######3.2.1.2.3. 附加 – 通过表单创建 (“podcast”)资源
	 
	/**
	 * Adds a new podcast (resource) from "form" (at least title and feed
	 * elements are required at the DB level)
	 * 
	 * @param title
	 * @param linkOnPodcastpedia
	 * @param feed
	 * @param description
	 * @return
	 * @throws AppException
	 */
	@POST
	@Consumes({ MediaType.APPLICATION_FORM_URLENCODED })
	@Produces({ MediaType.TEXT_HTML })
	@Transactional
	public Response createPodcastFromApplicationFormURLencoded(
	        @FormParam("title") String title,
	        @FormParam("linkOnPodcastpedia") String linkOnPodcastpedia,
	        @FormParam("feed") String feed,
	        @FormParam("description") String description) throws AppException {
	 
	    Podcast podcast = new Podcast(title, linkOnPodcastpedia, feed,
	            description);
	    Long createPodcastid = podcastService.createPodcast(podcast);
	 
	    return Response
	            .status(Response.Status.CREATED)// 201
	            .entity("A new podcast/resource has been created at /demo-rest-jersey-spring/podcasts/"
	                    + createPodcastid)
	            .header("Location",
	                    "http://localhost:8888/demo-rest-jersey-spring/podcasts/"
	                            + String.valueOf(createPodcastid)).build();
	}

注解

* @POST – 指示方法响应到 HTTP POST 请求
* @Consumes({MediaType.APPLICATION_FORM_URLENCODED}) – 定义方法可以接受的媒体类型，本例为"application/x-www-form-urlencoded"
* @FormParam – 这个注解绑定的表单参数值包含了请求对应资源方法参数的实体。值是 URL  的解码，除非 禁用 解码的注解。
* @Produces({MediaType.TEXT_HTML}) – 定义方法产生的媒体类型本例为 "text/html"

响应

* 成功: HTTP 状态 为 201 的 text/html 文件和头的位置指定的资源已被创建
* 错误:
	* 400：没有足够的数据提供
	* 409：冲突了。如果在服务器端被确定 具有相同的 podcast 的存在

####3.2.2. 读 podcast

#####3.2.2.1. 设计

API 支持两种操作

* 返回 podcast 的集合
* 根据 id 返回  podcast

<tbody align="center"><tr><td><b>&nbsp;Description</b></td><td><b>&nbsp;URI</b></td><td><b>&nbsp;HTTP method<br> </b></td><td><b>&nbsp;HTTP Status response</b></td><iframe id="tmp_downloadhelper_iframe" style="display: none;"></iframe></tr><tr><td>返回所有 podcast </td><td>&nbsp;/podcasts/?orderByInsertionDate={ASC|DESC}&amp;numberDaysToLookBack={val}</td><td>GET</td><td>200&nbsp;OK</td></tr><tr><td>&nbsp;添加新的 podcast (所有值都要传递)</td><td>&nbsp;/podcasts/{id}</td><td>GET</td><td>200&nbsp;OK</td></tr></tbody>

注意到集合资源的参数–rderByInsertionDate 和 numberDaysToLookBack。在URI查询参数添加过滤器而不是路径的一部分这个是很有道理的。

#####3.2.2.2. 实现

######3.2.2.2.1. 获取所有 podcasts (“/”)

	/**
	 * Returns all resources (podcasts) from the database
	 * 
	 * @return
	 * @throws IOException
	 * @throws JsonMappingException
	 * @throws JsonGenerationException
	 * @throws AppException
	 */
	@GET
	@Produces({ MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML })
	public List<Podcast> getPodcasts(
			@QueryParam("orderByInsertionDate") String orderByInsertionDate,
			@QueryParam("numberDaysToLookBack") Integer numberDaysToLookBack)
			throws JsonGenerationException, JsonMappingException, IOException,
			AppException {
		List<Podcast> podcasts = podcastService.getPodcasts(
				orderByInsertionDate, numberDaysToLookBack);
		return podcasts;
	}

注解

* @GET – 指示方法响应到 HTTP GET 请求
* @Produces({MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML}) – 定义方法可以接受的媒体类型，本例为"application/json" 或者 "application/xml"（在 Podcast 类前 添加  @XmlRootElement ），将返回  JSON 或者 XML 格式的 podcast 集合 

响应

* 成功: HTTP 状态 为 200 的 podcast 数据集合

######3.2.2.2.1. 读一个 podcast

根据 id  获取一个 podcast

	@GET
	@Path("{id}")
	@Produces({ MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML })
	public Response getPodcastById(@PathParam("id") Long id)
			throws JsonGenerationException, JsonMappingException, IOException,
			AppException {
		Podcast podcastById = podcastService.getPodcastById(id);
		return Response.status(200).entity(podcastById)
				.header("Access-Control-Allow-Headers", "X-extra-header")
				.allow("OPTIONS").build();
	}

注解

* @GET – 指示方法响应到 HTTP GET 请求
* @PathParam("id")- 绑定传递的参数值
* @Produces({MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML}) – 定义方法可以接受的媒体类型，本例为"application/json" 或者 "application/xml"（在 Podcast 类前 添加  @XmlRootElement ），将返回  JSON 或者 XML 格式的 podcast 集合 

响应

* 成功: HTTP 状态 为 200 的 podcast 
* 错误： 404 Not found。如果没有在数据库中找到

####3.2.3. 更新 podcast

#####3.2.3.1. 设计

<table cellspacing="0" cellpadding="5" border="1" align="center" style="width:600px"><tbody align="center"><tr><td><b>Description</b></td><td><b>URI</b></td><td><b>HTTP method<br> </b></td><td><b>HTTP Status response</b></td></tr><tr><td>更新 podcast (<strong>完全</strong>)</td><td>&nbsp;/podcasts/{id}</td><td>PUT</td><td>200&nbsp;OK</td></tr><tr><td>&nbsp;更新 podcast (<strong>部分</strong>)</td><td>&nbsp;/podcasts/{id}</td><td>POST</td><td>200&nbsp;OK</td></tr></tbody></table>

1.完全更新  – 提供所有的值
2.部分更新  – 传递部分属性值即可

#####3.2.3.1. 实现

######3.2.3.1.1. 完全更新

创建或者完全更新资源
	
	@PUT
	@Path("{id}")
	@Consumes({ MediaType.APPLICATION_JSON })
	@Produces({ MediaType.TEXT_HTML })
	public Response putPodcastById(@PathParam("id") Long id, Podcast podcast)
			throws AppException {
	
		Podcast podcastById = podcastService.verifyPodcastExistenceById(id);
	
		if (podcastById == null) {
			// resource not existent yet, and should be created under the
			// specified URI
			Long createPodcastId = podcastService.createPodcast(podcast);
			return Response
					.status(Response.Status.CREATED)
					// 201
					.entity("A new podcast has been created AT THE LOCATION you specified")
					.header("Location",
							"http://localhost:8888/demo-rest-jersey-spring/podcasts/"
									+ String.valueOf(createPodcastId)).build();
		} else {
			// resource is existent and a full update should occur
			podcastService.updateFullyPodcast(podcast);
			return Response
					.status(Response.Status.OK)
					// 200
					.entity("The podcast you specified has been fully updated created AT THE LOCATION you specified")
					.header("Location",
							"http://localhost:8888/demo-rest-jersey-spring/podcasts/"
									+ String.valueOf(id)).build();
		}
	}

注解

* @PUT – 指示方法响应到 HTTP PUT  请求
* @PathParam("id")- 绑定传递的参数值
* @Consumes({MediaType.APPLICATION_JSON}) – 定义方法可以接受的媒体类型，本例为"application/json"
* @Produces({MediaType.TEXT_HTML}) – 定义方法可以产生的媒体类型，本例为t"ext/html"

响应

* 创建
	* 成功: HTTP 状态 为 201 Created
	* 错误： 400 Bad Request。如果需要的属性值没有提供
* 完全更新：
	* 成功: HTTP 状态 为 200 
	* 错误： 400 Bad Request。如果不是所有的属性都提供

######3.2.3.1.2. 部分更新

	//PARTIAL update
	@POST
	@Path("{id}")	
	@Consumes({ MediaType.APPLICATION_JSON })
	@Produces({ MediaType.TEXT_HTML })
	public Response partialUpdatePodcast(@PathParam("id") Long id, Podcast podcast) throws AppException {
		podcast.setId(id);
		podcastService.updatePartiallyPodcast(podcast);
		return Response.status(Response.Status.OK)// 200
				.entity("The podcast you specified has been successfully updated")
				.build();	
	}

注解

* @POST – 指示方法响应到 HTTP POST 请求
* @PathParam("id")- 绑定传递的参数值
* @Consumes({MediaType.APPLICATION_JSON}) – 定义方法可以接受的媒体类型，本例为"application/json"
* @Produces({MediaType.TEXT_HTML}) – 定义方法可以产生的媒体类型，本例为t"ext/html"

响应

* 成功: HTTP 状态 为 200 OK
* 错误： 404 Not Found。如果资源不存在

####3.2.4. 删除 podcast

#####3.2.4.1. 设计

<table cellspacing="0" cellpadding="5" border="1" align="center" style="width:700px"><tbody align="center"><tr><td><b>Description</b></td><td><b>URI</b></td><td><b>HTTP method<br> </b></td><td><b>HTTP Status response</b></td></tr><tr><td>移除所有 podcasts</td><td>&nbsp;/podcasts/</td><td>DELETE</td><td>204 No content</td></tr><tr><td>移除特定位置的 podcast</td><td>&nbsp;/podcasts/{id}</td><td>DELETE</td><td>204 No content</td></tr></tbody></table>

#####3.2.4.2. 实现

######3.2.4.2.1. 删除所有资源

	@DELETE
	@Produces({ MediaType.TEXT_HTML })
	public Response deletePodcasts() {
		podcastService.deletePodcasts();
		return Response.status(Response.Status.NO_CONTENT)// 204
				.entity("All podcasts have been successfully removed").build();
	}

注解

* @DELETE – 指示方法响应到 HTTP DELETE  请求
* @Produces({MediaType.TEXT_HTML}) – 定义方法可以产生的媒体类型，本例为"text/html"

响应

* 返回 html  文档

######3.2.4.2.2. 删除一个资源

	@DELETE
	@Path("{id}")
	@Produces({ MediaType.TEXT_HTML })
	public Response deletePodcastById(@PathParam("id") Long id) {
		podcastService.deletePodcastById(id);
		return Response.status(Response.Status.NO_CONTENT)// 204
				.entity("Podcast successfully removed from database").build();
	}

注解

* @DELETE – 指示方法响应到 HTTP DELETE 请求
* @PathParam("id")- 绑定传递的参数值
* @Consumes({MediaType.APPLICATION_JSON}) – 定义方法可以接受的媒体类型，本例为"application/json"
* @Produces({MediaType.TEXT_HTML}) – 定义方法可以产生的媒体类型，本例为"text/html"

响应

* 成功: HTTP 状态 为 204 No Content
* 错误： 404 Not Found。如果资源不存在

##4. 日志

详见 [http://www.codingpedia.org/ama/how-to-log-in-spring-with-slf4j-and-logback/](http://www.codingpedia.org/ama/how-to-log-in-spring-with-slf4j-and-logback/)

##5. 异常处理

错误处理要有统一的格式，就像下面
	
	{
	   "status": 400,
	   "code": 400,
	   "message": "Provided data not sufficient for insertion",
	   "link": "http://www.codingpedia.org/ama/tutorial-rest-api-design-and-implementation-with-jersey-and-spring",
	   "developerMessage": "Please verify that the feed is properly generated/set"
	}

##6. 服务端添加 CORS 支持

##7. 测试

###7.1. 在Java集成测试

###7.1.1. 配置

#####7.1.1.1 Jersey 客户端依赖

	<dependency>
	    <groupId>org.glassfish.jersey.core</groupId>
	    <artifactId>jersey-client</artifactId>
	    <version>${jersey.version}</version>
	    <scope>test</scope>
	</dependency>

#####7.1.1.2. Failsafe 插件

	
	<plugins>
		[...]
	    <plugin>
	        <groupId>org.apache.maven.plugins</groupId>
	        <artifactId>maven-failsafe-plugin</artifactId>
	        <version>2.16</version>
	        <executions>
	            <execution>
	                <id>integration-test</id>
	                <goals>
	                    <goal>integration-test</goal>
	                </goals>
	            </execution>
	            <execution>
	                <id>verify</id>
	                <goals>
	                    <goal>verify</goal>
	                </goals>
	            </execution>
	        </executions>
	    </plugin>
		[...]
	</plugins>

#####7.1.1.2. Jetty Maven 插件

	<plugins>
		<plugin>
			<groupId>org.eclipse.jetty</groupId>
			<artifactId>jetty-maven-plugin</artifactId>
			<version>${jetty.version}</version>
			<configuration>
				<jettyConfig>${project.basedir}/src/main/resources/config/jetty9.xml</jettyConfig>
				<stopKey>STOP</stopKey>
				<stopPort>9999</stopPort>
				<stopWait>5</stopWait>
				<scanIntervalSeconds>5</scanIntervalSeconds>
			[...]
			</configuration>
			<executions>
				<execution>
					<id>start-jetty</id>
					<phase>pre-integration-test</phase>
					<goals>
						<!-- stop any previous instance to free up the port -->
						<goal>stop</goal>				
						<goal>run-exploded</goal>
					</goals>
					<configuration>
						<scanIntervalSeconds>0</scanIntervalSeconds>
						<daemon>true</daemon>
					</configuration>
				</execution>
				<execution>
					<id>stop-jetty</id>
					<phase>post-integration-test</phase>
					<goals>
						<goal>stop</goal>
					</goals>
				</execution>
			</executions>
		</plugin>
		[...]
	</plugins>

详细配置见源码中的 pom.xml

####7.1.2. 编译集成测试

使用 JUnit  作为测试框架。默认的 Failsafe 插件 自动包含所有测试类

* "**/IT*.java" – “IT”开头的文件.
* "**/*IT.java" – “IT”结尾的文件.
* "**/*ITCase.java" – “ITCase”结尾的文件.

创建了测试类 RestDemoServiceIT 

	public class RestDemoServiceIT {
	
		[....]
		@Test
		public void testGetPodcast() throws JsonGenerationException,
				JsonMappingException, IOException {
	
			ClientConfig clientConfig = new ClientConfig();
			clientConfig.register(JacksonFeature.class);
	
			Client client = ClientBuilder.newClient(clientConfig);
	
			WebTarget webTarget = client
					.target("http://localhost:8888/demo-rest-jersey-spring/podcasts/2");
	
			Builder request = webTarget.request(MediaType.APPLICATION_JSON);
	
			Response response = request.get();
			Assert.assertTrue(response.getStatus() == 200);
	
			Podcast podcast = response.readEntity(Podcast.class);
	
			ObjectMapper mapper = new ObjectMapper();
			System.out
					.print("Received podcast from database *************************** "
							+ mapper.writerWithDefaultPrettyPrinter()
									.writeValueAsString(podcast));
	
		}
	}

注意：

* 在客户也要注册 JacksonFeature ，这样才能解析 JSON格式
* 用 jetty 测试，端口 8888
* 期望 返回 200 状态 给我们的请求
* org.codehaus.jackson.map.ObjectMapper 帮助返回格式化的 JSON 

####7.1.3. 运行集成测试

运行 
	
	mvn verify

设置  `jetty.port` 属性到 8888,Eclipse  配置如下

![](http://99btgc01.info/uploads/2014/11/run-integration-tests-eclipse.png)

###7.2. 用 SoapUI 集成测试

[youtube视频教程](http://www.youtube.com/watch?v=XV7WW0bDy9c)（需翻墙）

##8. 版本管理

几个要点：

* URL:  “/v1/podcasts/{id}”
* Accept/Content-type header: application/json; version=1

在 路径中 加入 版本信息

	@Component
	@Path("/v1/podcasts")
	public class PodcastResource {...}


参考：

* [https://jersey.java.net/](https://jersey.java.net/)
* [https://github.com/waylau/Jersey-2.x-User-Guide](https://github.com/waylau/Jersey-2.x-User-Guide)
* [http://www.codingpedia.org/ama/tutorial-rest-api-design-and-implementation-in-java-with-jersey-and-spring/](http://www.codingpedia.org/ama/tutorial-rest-api-design-and-implementation-in-java-with-jersey-and-spring/)