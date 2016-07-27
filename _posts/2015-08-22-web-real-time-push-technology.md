---
layout: post
title: Web 实时推送技术的总结
date: 2015-08-22 01:08
author: admin
comments: true
categories: [Web]
tags: [Web,real-time,push]
---

随着 Web 的发展，用户对于 Web 的实时的要求也越来越高 ，比如，工业运行监控、Web 在线通讯、即时报价系统、在线游戏等，都需要将后台发生的变化主动地、实时地传送到浏览器端，而不需要用户手动地刷新页面。本文对过去和现在流行的 Web 实时推送技术进行了全面的总结。

<!-- more -->

在标准的 HTTP 请求-响应的情况下,客户端打开一个连接,发送一个 HTTP请求(例如 HTTP GET 请求)到服务端,然后接收到 HTTP 回来的响应，一旦这个响应完全被发送或者接收，服务端就关闭连接。当客户端需要请求所有数据时,这通常总是由一个客户发起。

![](http://99btgc01.info/uploads/2015/08/sse-real-time-web-00.jpg)

相反, 服务器推送就能让服务端异步地将数据从服务端推到客户端。当连接由客户端建立完成,服务端就提供数据,并决定新数据“块"可用时将其发送到客户端。下面是支持服务端到客户端推送的技术总览:

##  Web 推送技术的总结

* 插件提供 socket 方式：比如利用 Flash XMLSocket，Java Applet 套接口，Activex 包装的 socket。
	* 优点：原生 socket 的支持，与 PC 端的实现方式相似；
	* 缺点：浏览器端需要装相应的插件；与 js 进行交互时复杂
	* 例子：AS3 页游；Flash 聊天室

* Polling：轮询，重复发送新的请求到服务端。如果服务端没有新的数据，就发送适当的指示并关闭连接。然后客户端等待一段时间后,发送另一个请求(例如,一秒后)
	* 优点：实现简单，无需做过多的更改
	* 缺点：轮询的间隔过长，会导致用户不能及时接收到更新的数据；轮询的间隔过短，会导致查询请求过多，增加服务器端的负担。
	
![](http://99btgc01.info/uploads/2015/08/sse-real-time-web-01.jpg)

* Long-polling：长轮询，客户端发送一个请求到服务端，如果服务端没有新的数据，就保持住这个连接直到有数据。一旦服务端有了数据（消息）给客户端，它就使用这个连接发送数据给客户端。接着连接关闭。
	* 优点：比 Polling 做了优化，有较好的时效性
	* 缺点：需第三方库支持，实现较为复杂；每次连接只能发送一个数据，多个数据发送时耗费服务器性能
	* 例子：[commet4j](http://www.comet4j.org/)

![](http://99btgc01.info/uploads/2015/08/sse-real-time-web-02.jpg)

* 基于 iframe 及 htmlfile 的流（streaming）方式：iframe 流方式是在页面中插入一个隐藏的 iframe，利用其src属性在服务器和客户端之间创建一条长链接，服务器向 iframe 传输数据（通常是 HTML，内有负责插入信息的 javascript），来实时更新页面。
	* 优点：消息能够实时到达；
	* 缺点：服务器维持着长连接期会消耗资源；iframe 不规范的用法；数据推送过程会有加载进度条显示，界面体验不好
	
![](http://99btgc01.info/uploads/2015/08/sse-real-time-web-09%281%29.jpg)

* Server-Sent events：SSE 与 长轮询机制类似,区别是每个连接不只发送一个消息。客户端发送一个请求，服务端就保持这个连接直到有一个新的消息已经准备好了,那么它将消息发送回客户端,同时仍然保持这个连接是打开,这样这个连接就可以用于另一个可用消息的发送。一旦准备好了一个新消息,通过同一初始连接发送回客户端。客户端单独处理来自服务端传回的消息后不关闭连接。所以,SSE 通常重用一个连接处理多个消息(称为事件)。SSE 还定义了一个专门的媒体类型 [text/event-stream](http://www.w3.org/TR/2009/WD-eventsource-20091029/#text-event-stream),描述一个从服务端发送到客户端的简单格式。SSE 还提供在大多数现代浏览器里的标准 javascript 客户端 API 实现。关于 SSE 的更多信息,请参见 [SSE API 规范](http://www.w3.org/TR/2009/WD-eventsource-20091029/)。
	* 优点：HTML5 标准；实现较为简单；一个连接可以发送多个数据
	* 缺点：IE 不支持 EventSource(可以使用第三方的 js 库来解决) ；服务器只能单向推送数据到客户端
	* 例子：参见《[REST 实战](https://github.com/waylau/rest-in-action)》 中的《用 SSE 构建实时 Web 应用》一章

![](http://99btgc01.info/uploads/2015/08/sse-real-time-web-03.jpg)


* WebSocket: WebSocket 与上述技术都不同，因为它提供了一个真正的全双工连接。发起者是一个客户端，发送一个带特殊 HTTP 头的请求到服务端,通知服务器， HTTP 连接可能“升级”到一个全双工的 TCP/IP WebSocket 连接。如果服务端支持 WebSocket,它可能会选择升级到 WebSocket。一旦建立 WebSocket 连接,它可用于客户机和服务器之间的双向通信。客户端和服务器可以随意向对方发送数据。此时，新的 WebSocket 连接上的交互不再是基于 HTTP 协议了。 WebSocket 可以用于需要快速在两个方向上交换小块数据的在线游戏或任何其他应用程序。
	* 优点：HTML5 标准；大多数浏览器支持；真正全双工；性能强
	* 缺点：实现相对复杂；需要对 ws 协议专门处理
	* 例子：参见《[Netty 实现 WebSocket 聊天功能](http://www.waylau.com/netty-websocket-chat/) 》、《[WebSocket 和 Golang 实现聊天功能](http://www.waylau.com/go-websocket-chat/)》
	
![](http://99btgc01.info/uploads/2015/08/sse-real-time-web-04.jpg)



## SSE 与 WebSocket 对比

综合上面的各种技术，总结出，对于 Web 的实时应用来说，SSE 与 WebSocket 是最为可行的技术方案。而对于这两种技术的选型，则需要根据具体的应用场景。概括来说，WebSocket 能做的，SSE 也能做，反之亦然，但在完成某些任务方面，它们各有千秋。

WebSocket 是一种更为复杂的服务端实现技术，但它是真正的双向传输技术，既能从服务端向客户端推送数据，也能从客户端向服务端推送数据。

WebSocket 和 SSE 的浏览器支持率差不多,除了IE。IE是个例外，即便IE11都还不支持原生 SSE，IE10 添加了WebSocket 支持。当然，使用第三库可以实现了对上述两种技术在 IE 10 上面的应用。 

以下为 SSE 浏览器支持情况：

![](http://99btgc01.info/uploads/2015/08/sse-real-time-web-05.jpg)

以下为 WebSocket 浏览器支持情况：

![](http://99btgc01.info/uploads/2015/08/sse-real-time-web-06.jpg)

与 WebSocket 相比，SSE 有一些显著的优势。我认为它最大的优势就是便利：不需要添加任何新组件，用任何你习惯的后端语言和框架就能继续使用。你不用为新建虚拟机、弄一个新的IP或新的端口号而劳神，就像在现有网站中新增一个页面那样简单。我喜欢把这称为既存基础设施优势。

SSE 的第二个优势是服务端的简洁，服务端代码只需几行。相对而言，WebSocket 则很复杂，不借助辅助类库基本搞不定。

因为 SSE 能在现有的 HTTP/HTTPS 协议上运作，所以它能直接运行于现有的代理服务器和认证技术。而对 WebSocket 而言，代理服务器需要做一些开发（或其他工作）才能支持。SSE还有一个优势：它是一种文本协议，脚本调试非常容易。事实上，在本书中，我们会在开发和测试时用 curl，甚至直接在命令行中运行后端脚本。

不过，这就引出了 WebSocket 相较 SSE 的一个潜在优势：WebSocket 是二进制协议，而 SSE 是文本协议（通常使用UTF-8编码）。当然，我们可以通过 SSE 连接传输二进制数据：在 SSE 中，只有两个具有特殊意义的字符，它们是 CR 和LF，而对它们进行转码并不难。但用 SSE 传输二进制数据时数据会变大，如果需要从服务端到客户端传输大量的二进制数据，最好还是用 WebSocket。

WebSocket 相较 SSE 最大的优势在于它是双向交流的，这意味向服务端发送数据就像从服务端接收数据一样简单。用 SSE时，一般通过一个独立的 Ajax 请求从客户端向服务端传送数据。相对于 WebSocket，这样使用 Ajax 会增加开销，但也就多一点点而已。如此一来，问题就变成了“什么时候需要关心这个差异？”如果需要以1次/秒或者更快的频率向服务端传输数据，那应该用 WebSocket。0.2次/秒到1次/秒的频率是一个灰色地带，用 WebSocket 和用 SSE 差别不大；但如果你期望重负载，那就有必要确定基准点。频率低于0.2次/秒左右时，两者差别不大。

从服务端向客户端传输数据的性能如何？如果是文本数据而非二进制数据（如前文所提到的），SSE 和 WebSocket 没什么区别。它们都用 TCP/IP 套接字，都是轻量级协议。延迟、带宽、服务器负载等都没有区别。

在旧版本浏览器上的兼容，WebSocket 难兼容，SSE 易兼容。


## 参考

* [REST 实战](https://github.com/waylau/rest-in-action)
* [Jersey 2.x 用户指南](https://github.com/waylau/Jersey-2.x-User-Guide)
* [Netty 实战(精髓)](https://github.com/waylau/essential-netty-in-action)
* [SSE API 规范](http://www.w3.org/TR/2009/WD-eventsource-20091029/)
* Data Push Apps with HTML5 SSE（by Darren Cook）
* <http://www.ibm.com/developerworks/cn/web/wa-lo-comet/>
* <https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS>
* <http://stackoverflow.com/questions/11077857/what-are-long-polling-websockets-server-sent-events-sse-and-comet>
