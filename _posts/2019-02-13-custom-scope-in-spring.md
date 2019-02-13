---
layout: post
title: 在 Spring 中自定义 scope
date: 2019-02-13 00:22
author: admin
comments: true
categories: [Spring]
tags: [Spring]
---

大家对于 Spring 的 scope 应该都不会默认。所谓 scope，字面理解就是“作用域”、“范围”，如果一个 bean 的 scope 配置为 singleton，则从容器中获取 bean 返回的对象都是相同的；如果 scope 配置为prototype，则每次返回的对象都不同。

一般情况下，Spring 提供的 scope 都能满足日常应用的场景。但如果你的需求极其特殊，则本文所介绍自定义 scope 合适你。


<!-- more -->

## Spring 内置的 scope


默认时，所有 Spring bean 都是的单例的，意思是在整个 Spring 应用中，bean的实例只有一个。可以在 bean 中添加 scope 属性来修改这个默认值。scope 属性可用的值如下：


范围 | 描述
----|----
singleton | 每个 Spring 容器一个实例（默认值）
prototype | 允许 bean 可以被多次实例化（使用一次就创建一个实例）
request	| 定义 bean 的 scope 是 HTTP 请求。每个 HTTP 请求都有自己的实例。只有在使用有 Web 能力的 Spring 上下文时才有效
session	| 定义 bean 的 scope 是 HTTP 会话。只有在使用有 Web 能力的 Spring ApplicationContext 才有效
application | 定义了每个 ServletContext 一个实例
websocket | 定义了每个 WebSocket 一个实例。只有在使用有 Web 能力的 Spring ApplicationContext 才有效


如果上述 scope 仍然不能满足你的需求，Spring 还预留了接口，允许你自定义 scope。



## Scope 接口

`org.springframework.beans.factory.config.Scope`接口用于定义scope的行为：

```java
package org.springframework.beans.factory.config;

import org.springframework.beans.factory.ObjectFactory;
import org.springframework.lang.Nullable;

public interface Scope {

	Object get(String name, ObjectFactory<?> objectFactory);

	@Nullable
	Object remove(String name);

	void registerDestructionCallback(String name, Runnable callback);

	@Nullable
	Object resolveContextualObject(String key);

	@Nullable
	String getConversationId();

}
```

一般来说，只需要重新 get 和 remove 方法即可。

## 自定义线程范围内的scope

现在进入实战环节。我们要自定义一个Spring没有的scope，该scope将bean的作用范围限制在了线程内。即，相同线程内的bean是同个对象，跨线程则是不同的对象。


### 1. 定义scope

要自定义一个Spring的scope，只需实现 `org.springframework.beans.factory.config.Scope`接口。代码如下：

```java
package com.waylau.spring.scope;

import java.util.HashMap;
import java.util.Map;

import org.springframework.beans.factory.ObjectFactory;
import org.springframework.beans.factory.config.Scope;

/**
 * Thread Scope.
 * 
 * @since 1.0.0 2019年2月13日
 * @author <a href="https://waylau.com">Way Lau</a>
 */
public class ThreadScope implements Scope {
	
	private final ThreadLocal<Map<String, Object>> threadLoacal = new ThreadLocal<Map<String, Object>>() {
		@Override
		protected Map<String, Object> initialValue() {
			return new HashMap<String, Object>();
		}
	};

	public Object get(String name, ObjectFactory<?> objectFactory) {
		Map<String, Object> scope = threadLoacal.get();
		Object obj = scope.get(name);

		// 不存在则放入ThreadLocal
		if (obj == null) {
			obj = objectFactory.getObject();
			scope.put(name, obj);
			
			System.out.println("Not exists " + name + "; hashCode: " + obj.hashCode());
		} else {
			System.out.println("Exists " + name + "; hashCode: " + obj.hashCode());
		}

		return obj;
	}

	public Object remove(String name) {
		Map<String, Object> scope = threadLoacal.get();
		return scope.remove(name);
	}

	public String getConversationId() {
		return null;
	}

	public void registerDestructionCallback(String arg0, Runnable arg1) {
	}

	public Object resolveContextualObject(String arg0) {
		return null;
	}

}
```

在上述代码中，threadLoacal用于做线程之间的数据隔离。换言之，threadLoacal实现了相同的线程相同名字的bean是同一个对象；不同的线程的相同名字的bean是不同的对象。

同时，我们将对象的hashCode打印了出来。如果他们是相同的对象，则hashCode是相同的。


### 2. 注册scope

定义一个AppConfig配置类，将自定义的scope注册到容器中去。代码如下：

```java
package com.waylau.spring.scope;

import java.util.HashMap;
import java.util.Map;

import org.springframework.beans.factory.config.CustomScopeConfigurer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

/**
 * App Config.
 *
 * @since 1.0.0 2019年2月13日
 * @author <a href="https://waylau.com">Way Lau</a>
 */
@Configuration
@ComponentScan
public class AppConfig {

	@Bean
	public static CustomScopeConfigurer customScopeConfigurer() {
		CustomScopeConfigurer customScopeConfigurer = new CustomScopeConfigurer();

		Map<String, Object> map = new HashMap<String, Object>();
		map.put("threadScope", new ThreadScope());

		// 配置scope
		customScopeConfigurer.setScopes(map);
		return customScopeConfigurer;
	}
}
```

“threadScope”就是自定义ThreadScope的名称。



### 3. 使用scope

接下来就根据一般的scope的用法，来使用自定义的scope了。代码如下：

```java
package com.waylau.spring.scope.service;

import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Service;

/**
 * Message Service Impl.
 * 
 * @since 1.0.0 2019年2月13日
 * @author <a href="https://waylau.com">Way Lau</a>
 */
@Scope("threadScope")
@Service
public class MessageServiceImpl implements MessageService {
	
	public String getMessage() {
		return "Hello World!";
	}

}
```

其中`@Scope("threadScope")`中的“threadScope”就是自定义ThreadScope的名称。


### 4. 定义应用入口

定义Spring应用入口：

```java
package com.waylau.spring.scope;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import com.waylau.spring.scope.service.MessageService;

/**
 * Application Main.
 * 
 * @since 1.0.0 2019年2月13日
 * @author <a href="https://waylau.com">Way Lau</a>
 */
public class Application {

	public static void main(String[] args) {
		@SuppressWarnings("resource")
		ApplicationContext context = 
            new AnnotationConfigApplicationContext(AppConfig.class);

		MessageService messageService = context.getBean(MessageService.class);
		messageService.getMessage();
 
		MessageService messageService2 = context.getBean(MessageService.class);
		messageService2.getMessage();
		
	}

}
```

运行应用观察控制台输出如下：

```
Not exists messageServiceImpl; hashCode: 2146338580
Exists messageServiceImpl; hashCode: 2146338580
```

输出的结果也就验证了ThreadScope“相同的线程相同名字的bean是同一个对象”。


如果想继续验证ThreadScope“不同的线程的相同名字的bean是不同的对象”，则只需要将Application改造为多线程即可。

```java
package com.waylau.spring.scope;

import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import com.waylau.spring.scope.service.MessageService;

/**
 * Application Main.
 * 
 * @since 1.0.0 2019年2月13日
 * @author <a href="https://waylau.com">Way Lau</a>
 */
public class Application {

	public static void main(String[] args) throws InterruptedException, ExecutionException {
		@SuppressWarnings("resource")
		ApplicationContext context = 
			new AnnotationConfigApplicationContext(AppConfig.class);

		CompletableFuture<String> task1 = CompletableFuture.supplyAsync(()->{
            //模拟执行耗时任务
            MessageService messageService = context.getBean(MessageService.class);
			messageService.getMessage();
	 
			MessageService messageService2 = context.getBean(MessageService.class);
			messageService2.getMessage();
            //返回结果
            return "result";
        });
		
		CompletableFuture<String> task2 = CompletableFuture.supplyAsync(()->{
            //模拟执行耗时任务
            MessageService messageService = context.getBean(MessageService.class);
			messageService.getMessage();
	 
			MessageService messageService2 = context.getBean(MessageService.class);
			messageService2.getMessage();
            //返回结果
            return "result";
        });
		
		task1.get();
		task2.get();
		
		
	}

}
```

观察输出结果;

```
Not exists messageServiceImpl; hashCode: 1057328090
Not exists messageServiceImpl; hashCode: 784932540
Exists messageServiceImpl; hashCode: 1057328090
Exists messageServiceImpl; hashCode: 784932540
```

上述结果验证ThreadScope“相同的线程相同名字的bean是同一个对象；不同的线程的相同名字的bean是不同的对象”


## 源码

见<https://github.com/waylau/spring-5-book> 的 `s5-ch02-custom-scope-annotation`项目。

## 参考引用


* 原文同步至:<https://waylau.com/custom-scope-in-spring/>
* 《Spring 5 开发大全》:<https://search.jd.com/Search?keyword=%E6%9F%B3%E4%BC%9F%E5%8D%AB%20Spring%205%20%E5%BC%80%E5%8F%91%E5%A4%A7%E5%85%A8&enc=utf-8&wq=%E6%9F%B3%E4%BC%9F%E5%8D%AB%20Spring%205%20%E5%BC%80%E5%8F%91%E5%A4%A7%E5%85%A8&pvid=23b0c41b891e439aaf944e7d08dedfdd>