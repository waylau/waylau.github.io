---
layout: post
title:  在flex中监听鼠标右键事件,提示 TypeError Error 2007 参数type不能为空。
date:   2013-03-11 00:50
author: admin
comments: true
categories: [Flash]
tags: [Flash]
---

在flex中监听鼠标右键事件 ，报错，提示 缺少参数、

TypeError: Error 2007: 参数 type 不能为空。

```
at flash.events::EventDispatcher/addEventListener()
at com.waylau.eagleos.components::DesktopExplorer/service_resultHandler()[D:\workspaceFB47\com.waylau.eagleos_0.9.5\src\com\waylau\eagleos\components\DesktopExplorer.mxml:59]
at com.waylau.eagleos.components::DesktopExplorer/__service_result()[D:\workspaceFB47\com.waylau.eagleos_0.9.5\src\com\waylau\eagleos\components\DesktopExplorer.mxml:214]
at flash.events::EventDispatcher/dispatchEventFunction()
at flash.events::EventDispatcher/dispatchEvent()
at HTTPOperation/http://www.adobe.com/2006/flex/mx/internal::dispatchRpcEvent()[E:\dev\4.y\frameworks\projects\rpc\src\mx\rpc\http\HTTPService.as:993]
at mx.rpc::AbstractInvoker/http://www.adobe.com/2006/flex/mx/internal::resultHandler()[E:\dev\4.y\frameworks\projects\rpc\src\mx\rpc\AbstractInvoker.as:318]
at mx.rpc::Responder/result()[E:\dev\4.y\frameworks\projects\rpc\src\mx\rpc\Responder.as:56]
at mx.rpc::AsyncRequest/acknowledge()[E:\dev\4.y\frameworks\projects\rpc\src\mx\rpc\AsyncRequest.as:84]
at DirectHTTPMessageResponder/completeHandler()[E:\dev\4.y\frameworks\projects\rpc\src\mx\messaging\channels\DirectHTTPChannel.as:451]
at flash.events::EventDispatcher/dispatchEventFunction()
at flash.events::EventDispatcher/dispatchEvent()
at flash.net::URLLoader/onComplete()
```
据悉，flash player 11.2版本以后支持鼠标右键，

所以开发的项目中运行时版本要相应设置，增加参数 `-swf-version=15` 以支持右键 ，即可