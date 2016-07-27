---
layout: post 
title: 用Jersey构建RESTful服务1--HelloWorld 
date: 2014-03-17 03:04 
author: admin 
comments: true
categories: [Java, Jersey, REST]
tags: [Java, Jersey, RESTful]
---
#一、环境 
* 1.Eclipse Juno R2 
* 2.Tomcat 7 
* 3.Jersey 2.x(最新2.11版本测试通过)  下载地址（ <https://jersey.java.net/download.html>）

#二、流程 
* 1.Eclipse 中创建一个 Dynamic Web Project ,本例为“RestDemo”
* 2.按个各人习惯建好包，本例为“com.waylau.rest.resources”
<img src="http://b.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=7807f0deb119ebc4c478769cb21dbec1/0b55b319ebc4b745429f78adcdfc1e178a821505.jpg?referer=0b4df88379f40ad14cf3f2d3531c&x=.jpg"
/>
* 3.解压jaxrs-ri-2.7，将api、ext、lib文件夹下的jar包都放到项目的lib下；
<img src="http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=41a40894bc096b6385195e553c08f679/f31fbe096b63f6242082f6b88544ebf81b4ca3aa.jpg?referer=dc3f5bcfaf3459829c9dd1a20d68&x=.jpg"/>
项目引入jar包
<img src="http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=5011985bf503918fd3d13dcf610657aa/63d0f703918fa0ec3bccabb6249759ee3d6ddb06.jpg?referer=1b02ce10013b5bb5e7c015ce251c&x=.jpg"/>
* 4.在resources包下建一个class“HelloResource”

```java

	package com.waylau.rest.resources; 
	import javax.ws.rs.GET; 
	import javax.ws.rs.Path; 
	import javax.ws.rs.Produces; 
	import javax.ws.rs.PathParam; 
	import javax.ws.rs.core.MediaType; 
	@Path("/hello") 
	public class HelloResource { 
		@GET @Produces(MediaType.TEXT_PLAIN) 
		public String sayHello() 
		{ 
			return "Hello World!" ; 
		}
		
		@GET @Path("/{param}") 
		@Produces("text/plain;charset=UTF-8") 
		public String sayHelloToUTF8(@PathParam("param") String username) 
		{ 
			return "Hello " + username; 
		}
		
	}
```

* 5.修改web.xml,添加基于Servlet-的部署

```xml
<servlet> 
	<servlet-name>Way REST Service</servlet-name> <servlet-class>org.glassfish.jersey.servlet.ServletContainer</servlet-class> 
	<init-param> 
	<param-name>jersey.config.server.provider.packages</param-name> 
	<param-value>com.waylau.rest.resources</param-value> </init-param> 
	<load-on-startup>1</load-on-startup> 
</servlet>

<servlet-mapping> 
	<servlet-name>Way REST Service</servlet-name> 
	<url-pattern>/rest/*</url-pattern> 
</servlet-mapping>
```
* 6.项目部署到tomcat,运行 
* 7.浏览器输入要访问的uri地址 <http://localhost:8089/RestDemo/rest/hello>,输出Hello World!
<img src="http://c.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=32792b8bb8a1cd1101b672258929b9c1/d000baa1cd11728b8db54399cafcc3cec2fd2cf5.jpg?referer=02302bc8af4bd1135dda83028cab&x=.jpg"/>

<http://localhost:8089/RestDemo/rest/hello/Way你好吗>,输出Hello Way你好吗
<img src="http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=9608ef4534d3d539c53d0fc60abc986d/14ce36d3d539b6006511c00ceb50352ac75cb7aa.jpg?referer=240eae6c34d12f2e97129a50f168&x=.jpg"/>

参考：<https://jersey.java.net/documentation/latest/user-guide.html>

**本章源码**：[https://github.com/waylau/RestDemo/tree/master/jersey-demo1-helloworld](https://github.com/waylau/RestDemo/tree/master/jersey-demo1-helloworld)