---
layout: post
title: Jersey “Auto-Discoverable Features” 在 Maven 编译后失效
date: 2015-09-18 01:08
author: admin
comments: true
categories: [Jersey,Maven]
tags: [Jersey,Maven]
---

## 问题

原本 Jersey "Auto-Discoverable Features"(自动发现功能)中 JSON 解析在 eclipse　右键运行项目是正常的，但用 Maven 编译成 jar 后运行，就无法使用 JSON 解析了

<!-- more -->

## Jersey "Auto-Discoverable Features"

Jersey "Auto-Discoverable Features"(自动发现功能)。默认情况下 Jersey 2.x 不隐式注册在 classpath 上可用的模块中的任何扩展功能，除非明确在扩展文档中进行说明。用户将明确注册的扩展[功能](http://jax-rs-spec.java.net/nonav/$%7Bjaxrs.api.version%7D/apidocs/javax/ws/rs/core/Feature.html)来使用他们的 Application 子类。一小部分 Jersey 提供模块不需要显式注册他们的扩展功能，因为这些在[配置](http://jax-rs-spec.java.net/nonav/$%7Bjaxrs.api.version%7D/apidocs/javax/ws/rs/core/Configuration.html)（客户端/服务器）中将会被Jersey 运行时发现和注册，这些功能模块实现的这些特性将呈现在 JAX-RS 应用部署的 classpath 上。这些自动发现模块包括：

* 来自 jersey-media-moxy 的 JSON 绑定特性
* jersey-media-json-processing
* jersey-bean-validation

也就是说，当我使用 jersey-media-moxy 的 JSON 解析时，理论上我并不需要注册 JSON 解析。其中 jersey-media-moxy 的依赖为：

	<dependency>
		<groupId>org.glassfish.jersey.media</groupId>
		<artifactId>jersey-media-moxy</artifactId>
		<version>2.21</version>
	</dependency>

## 解决

也许是 Maven 编译机制的问题，程序无法自动发现功能，可以采用手动注册的方式，在 ResourceConfig 配置中进行注册 MoxyJsonFeature，如下：

	/**
	 * REST 主应用
	 * 
	 * @author waylau.com
	 * 2015年9月18日
	 */
	public class RestApplication extends ResourceConfig {
	
		public RestApplication() {
			// 资源类所在的包路径  
		    packages("com.waylau.rest.resource");
		    
		    // 注册 MultiPart
		    register(MultiPartFeature.class);
		    
		    // 注册 CORS过滤器
		    register(CrossDomainFilter.class);
		    
		    // 注册 JSON
		    register(MoxyJsonFeature.class);
		    
		    // 注册 SSE
		    register(SseFeature.class);
		}
	}

## 参考

* 《[Jersey 2.x 用户指南](https://github.com/waylau/Jersey-2.x-User-Guide)》 第四章
