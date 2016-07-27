---
layout: post 
title: 用Jersey构建RESTful服务3--JAVA对象转成JSON输出
date: 2014-03-17 05:04 
author: admin 
comments: true
categories: [Java, Jersey, REST]
tags: [Java, Jersey, RESTful]
---
#一、 总体说明

XML和JSON 是最为常用的数据交换格式。本例子演示如何将java对象，转成JSON输出。

#二、流程

* 1.在上文项目中，
在“com.waylau.rest.resources.UserResource“中增加代码，代码如下：

```java
	@GET    
	@Path("/getUserJson")    
	@Produces(MediaType.APPLICATION_JSON)    
	public User getUserJson() {    
	 User user  = new User();    
	 user.setAge("27");    
	 user.setUserId("005");    
	 user.setUserName("Fmand");    
	 return user;    
	}   
```

MediaType.APPLICATION_JSON 说明输出的是JSON格式

* 2,运行项目，浏览器输入<http://localhost:8089/RestDemo/rest/users/getUserJson>
期望获取到json的数据，此时，项目报错

```java
	org.glassfish.jersey.message.internal.MessageBodyProviderNotFoundException: MessageBodyWriter not found for media type=application/json, type=class com.waylau.rest.bean.User, genericType=class com.waylau.rest.bean.User.  
	    at org.glassfish.jersey.message.internal.WriterInterceptorExecutor$TerminalWriterInterceptor.aroundWriteTo(WriterInterceptorExecutor.java:247)  
	    at org.glassfish.jersey.message.internal.WriterInterceptorExecutor.proceed(WriterInterceptorExecutor.java:162)  
	    at org.glassfish.jersey.filter.LoggingFilter.aroundWriteTo(LoggingFilter.java:293)  
	    at org.glassfish.jersey.message.internal.WriterInterceptorExecutor.proceed(WriterInterceptorExecutor.java:162)  
	    at org.glassfish.jersey.server.internal.JsonWithPaddingInterceptor.aroundWriteTo(JsonWithPaddingInterceptor.java:103)  
	    at org.glassfish.jersey.message.internal.WriterInterceptorExecutor.proceed(WriterInterceptorExecutor.java:162)  
	    at org.glassfish.jersey.server.internal.MappableExceptionWrapperInterceptor.aroundWriteTo(MappableExceptionWrapperInterceptor.java:88)  
	    at org.glassfish.jersey.message.internal.WriterInterceptorExecutor.proceed(WriterInterceptorExecutor.java:162)  
	    at org.glassfish.jersey.message.internal.MessageBodyFactory.writeTo(MessageBodyFactory.java:1154)  
	    at org.glassfish.jersey.server.ServerRuntime$Responder.writeResponse(ServerRuntime.java:571)  
	    at org.glassfish.jersey.server.ServerRuntime$Responder.processResponse(ServerRuntime.java:378)  
	    at org.glassfish.jersey.server.ServerRuntime$Responder.process(ServerRuntime.java:368)  
	    at org.glassfish.jersey.server.ServerRuntime$1.run(ServerRuntime.java:262)  
```

<img src="http://g.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=5f3758b36509c93d03f20ef2af0689e1/7e3e6709c93d70cfade7b8b0fadcd100baa12b69.jpg?referer=70ed5c049c510fb3210e42a79527&x=.jpg"/>
此时，需要获取json转换包的支持 。
可以由多种方式实现：MOXy、JSON-P、Jackson、Jettison等，本例为Jackson。

* 3. jackson-all-1.9.11.jar 下载地址http://wiki.fasterxml.com/JacksonDownload
* 4. 项目中引入jackson-all-1.9.11.jar
* 5.在“com.waylau.rest”目录下创建RestApplication.java

```java
package com.waylau.rest;  
  
import org.codehaus.jackson.jaxrs.JacksonJsonProvider;  
import org.glassfish.jersey.filter.LoggingFilter;  
import org.glassfish.jersey.server.ResourceConfig;  
   
/** 
 * 应用 
 * @author waylau.com 
 * 2014-3-18 
 */  
public class RestApplication extends ResourceConfig {  
    public RestApplication() {  
   
     //服务类所在的包路径  
     packages("com.waylau.rest.resources");  
     //注册JSON转换器  
     register(JacksonJsonProvider.class);  
   
    }  
}  
```

* 6.修改web.xml，初始化从RestApplicaton进入应用，如下：

```xml
<servlet>    
     <servlet-name>Way REST Service</servlet-name>  
     <servlet-class>org.glassfish.jersey.servlet.ServletContainer</servlet-class>  
      <init-param>  
           <param-name>javax.ws.rs.Application</param-name>  
           <param-value>com.waylau.rest.RestApplication</param-value>  
       </init-param>  
       
    <load-on-startup>1</load-on-startup>  
  </servlet>  
    
  <servlet-mapping>  
    <servlet-name>Way REST Service</servlet-name>  
    <url-pattern>/rest/*</url-pattern>  
  </servlet-mapping>  
```
* 7.运行项目，再次访问<http://localhost:8089/RestDemo/rest/users/getUserJson>
即可输出JSON文本
<img src="http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=d3225f7df536afc30a0c3f6083229af9/79f0f736afc37931da389494e9c4b74543a9113e.jpg?referer=796c6b856c061d95245103085fd5&x=.jpg"/>


**本章源码**：[https://github.com/waylau/RestDemo/tree/master/jersey-demo3-json](https://github.com/waylau/RestDemo/tree/master/jersey-demo3-json)
