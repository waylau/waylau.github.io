---
layout: post
title: Jersey REST 服务中 DELETE 请求无法接收 entity body 作为参数
date: 2015-09-21 01:08
author: admin
comments: true
categories: [Jersey,HTTP]
tags: [Jersey,HTTP]
---

## 问题

在用 Jersey 构建 REST 服务时，当试图用 Ajax 传递一个 JSON 对象 作为参数执行 DELETE 方法时，后台接收到的对象为 null.

<!-- more -->
 
服务器的资源类写法为

    @DELETE
    @Path("pojojson")
    @Consumes(MediaType.APPLICATION_JSON)
    public String deletePojoJsonBody(@QueryParam("name") String name ) {
        return  "You delete " + name;
    }

由下图，可以看到调用该接口时，接受到的参数为 null

![](http://99btgc01.info/uploads/2015/09/001%281%29.jpg)


## 原因

在新版的 HTTP 1.1 协议里面有如下描述：

> A payload within a DELETE request message has no defined semantics; sending a payload body on a DELETE request might cause some existing implementations to reject the request.

中文意思是：

> DELETE 请求消息中的 payload (metadata 和 body content) 没有定义语义；在DELETE 请求上发送一个 payload body  可能会导致一些现有的实现拒绝该请求。

即，不应该在 DELETE 请求中使用 entity body 来传递参数.

## 解决

修改代码，将参数写在 URI 里面即可：
 
    @DELETE
    @Path("pojojson")
    @Produces(MediaType.TEXT_PLAIN)
    public String deletePojoJson(@QueryParam("name") String name ) {
        return  "You delete " + name;
    }
  
![](http://99btgc01.info/uploads/2015/09/002%281%29.jpg)  

## 参考

* <http://stackoverflow.com/questions/299628/is-an-entity-body-allowed-for-an-http-delete-request>
* <http://tools.ietf.org/html/draft-ietf-httpbis-p2-semantics-26>
