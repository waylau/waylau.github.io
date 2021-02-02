---
layout: post
title: Netty——ChannelHandler之概述
date: 2019-12-18 01:41
author: admin
comments: true
categories: [Netty]
tags: [Netty,ChannelHandler]
---
 
ChannelHandler（管道处理器）其工作模式类似于Java Servlet过滤器，负责对I/O事件或者I/O操作进行拦截处理。采用事件的好处是，ChannelHandler可以选择自己感兴趣的事件进行处理，也可以对不感兴趣的事件进行透传或者终止。


<!-- more -->


 

## ChannelHandler接口
 

基于ChannelHandler接口，用户可以方便实现自己的业务，比如记录日志、编解码、数据过滤等。ChannelHandler接口定义如下：

 
```java
package io.netty.channel;
import io.netty.util.Attribute;
import io.netty.util.AttributeKey;
import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

 

public interface ChannelHandler {

    void handlerAdded(ChannelHandlerContext ctx) throws Exception;

    void handlerRemoved(ChannelHandlerContext ctx) throws Exception;

    void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception;

    @Inherited
    @Documented
    @Target(ElementType.TYPE)
    @Retention(RetentionPolicy.RUNTIME)
    @interface Sharable {

    }

}
```
 

 

ChannelHandler接口定义比如简单，只有三个方法：

 

* handlerAdded方法在ChannelHandler被添加到实际上下文中并准备好处理事件后调用。
* handlerRemoved方法在ChannelHandler从实际上下文中移除后调用，表明它不再处理事件。
* exceptionCaught方法会在抛出Throwable类后调用。

 

还有一个Sharable注解，该注解用于表示多个ChannelPipeline可以共享同一个ChannelHandler。

 

正式因为ChannelHandler接口过于简单，我们在实际开发中，不会直接实现ChannelHandler接口，因此，Netty提供了ChannelHandlerAdapter抽象类。

## ChannelHandlerAdapter抽象类

ChannelHandlerAdapter抽象类核心代码如下：

 
```java
package io.netty.channel;

import io.netty.util.internal.InternalThreadLocalMap;
import java.util.Map;
import java.util.WeakHashMap;

public abstract class ChannelHandlerAdapter implements ChannelHandler {

    boolean added;

    public boolean isSharable() {
        Class<?> clazz = getClass();
        Map<Class<?>, Boolean> cache = InternalThreadLocalMap.get().handlerSharableCache();
        Boolean sharable = cache.get(clazz);

        if (sharable == null) {
            sharable = clazz.isAnnotationPresent(Sharable.class);
            cache.put(clazz, sharable);
        }
        return sharable;
    }

    @Override
    public void handlerAdded(ChannelHandlerContext ctx) throws Exception {
        // NOOP
    }

    @Override
    public void handlerRemoved(ChannelHandlerContext ctx) throws Exception {
        // NOOP
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        ctx.fireExceptionCaught(cause);
    }

}
```

ChannelHandlerAdapter对exceptionCaught方法做了实现，并提供了isSharable方法。需要注意的是，ChannelHandlerAdapter是抽象类，用户可以自由的选择是否要覆盖ChannelHandlerAdapter类的实现。如果对某个方法感兴趣，直接覆盖掉这个方法即可，这样代码就变得简单清晰。

 

ChannelHandlerAdapter抽象类提供了两个子类ChannelInboundHandlerAdapter、ChannelOutboundHandlerAdapter用于针对出站事件、入站事件的进行处理。其中ChannelInboundHandlerAdapter实现了ChannelInboundHandler接口，而ChannelOutboundHandlerAdapter实现了ChannelOutboundHandler接口。

 

在实际开发过程中，我们的自定义的ChannelHandler多数是继承自ChannelInboundHandlerAdapter和ChannelOutboundHandlerAdapter类或者是这两个类的子类。比如在前面章节中所涉及的编解码器ByteToMessageDecoder、MessageToMessageDecoder、MessageToByteEncoder、MessageToMessageEncoder等，就是这两个类的子类。
## 参考引用

* 原文同步至<https://waylau.com/netty-channel>
* [《Netty原理解析与开发实战》](https://item.jd.com/13072504.html)