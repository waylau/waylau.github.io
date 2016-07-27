---
layout: post
title: WebSocket 和 Golang 实现聊天功能
date: 2014-11-17 01:41
author: admin
comments: true
categories: [Golang,WebSocket]
tags: [Golang,WebSocket]
---

这个示例应用程序展示了如何使用 [WebSocket](http://tools.ietf.org/html/rfc6455), [Golang](http://golang.org/) 和  [jQuery](http://jquery.com/) 创建一个简单的web聊天应用程序。这个示例的源代码在 [https://github.com/waylau/goChat](https://github.com/waylau/goChat) 。

<!-- more -->

##Running the example 运行示例

这个示例需要 Golang 开发环境。 该[页面](http://golang.org/doc/install.html)描述如何安装开发环境。

一旦你去启动和运行,您可以下载、构建和运行的例子, 使用命令:

	go get gary.burd.info/go-websocket-chat
	go-websocket-chat

在支持 websocket 的浏览器尝试打开 [http://127.0.0.1:8080/](http://127.0.0.1:8080/) 启动应用

##Server 服务器

服务器程序实现了 [http](http://golang.org/pkg/net/http/) 包，包含了 Go 分发和 Gorilla 项目的 websocket 包.

应用程序定义了两种类型, connection 和 hub 。服务器为每个 webscocket 连接 创建的一个 connection 类型的实例 。 连接器扮演了 websocket 和 hub 类型单例 之间的媒介 。 hub 保持一组注册了的连接器 和 广播到连接器的信息。

程序运行了一个 goroutine 给 hub ,两个 goroutine 给每个连接器。 goroutine 通过 channel 和其他进行交流。 hub 拥有注册连接器、注销连接器和广播信息的 channel。一个连机器拥有缓存的发出信息的 channel 。其中一个 连接器的 goroutine 从这个 channel 中读信息 并把信息写入 webscoket。另外一个连接器 goroutine 从 websocket  读信息，并把信息发送到 hub。

下面是 hub 类型代码：

	package main

	type hub struct {
	    // 注册了的连接器
	    connections map[*connection]bool
	
	    // 从连接器中发入的信息
	    broadcast chan []byte
	
	    // 从连接器中注册请求
	    register chan *connection
	
	    // 从连接器中注销请求
	    unregister chan *connection
	}
	
	var h = hub{
	    broadcast:   make(chan []byte),
	    register:    make(chan *connection),
	    unregister:  make(chan *connection),
	    connections: make(map[*connection]bool),
	}
	
	func (h *hub) run() {
	    for {
	        select {
	        case c := <-h.register:
	            h.connections[c] = true
	        case c := <-h.unregister:
	            if _, ok := h.connections[c]; ok {
	                delete(h.connections, c)
	                close(c.send)
	            }
	        case m := <-h.broadcast:
	            for c := range h.connections {
	                select {
	                case c.send <- m:
	                default:
	                    delete(h.connections, c)
	                    close(c.send)
	                }
	            }
	        }
	    }
	}

应用程序的 主要 函数启动 hub 以 goroutine 形式运行方法。连接器 发送请求到 hub 通过 注册、注销和广播 channel。

hub 注册连接器通过添加 connection 的指针作为 connections map 的主键。这个  map 的值通常是 true。

注销的代码有点复杂。除了从 connections map 删除连接器的指针外,  hub 关闭了 connection 的发送，来标识没有信息再被发送到 connection了。

hub 通过循环注册连接器和发送信息到连接器的发送 channel 来控制信息。 如果连接器的发送缓冲区已经满了，那么  hub 假设 客户端已死或卡住了。这种情况下, hub 注销连接器 并关闭 websocket.

下面关于 connection 类型的代码：

	package main

	import (
	    "github.com/gorilla/websocket"
	    "net/http"
	)
	
	type connection struct {
	    // websocket 连接器
	    ws *websocket.Conn
	
	    // 发送信息的缓冲 channel 
	    send chan []byte
	}
	
	func (c *connection) reader() {
	    for {
	        _, message, err := c.ws.ReadMessage()
	        if err != nil {
	            break
	        }
	        h.broadcast <- message
	    }
	    c.ws.Close()
	}
	
	func (c *connection) writer() {
	    for message := range c.send {
	        err := c.ws.WriteMessage(websocket.TextMessage, message)
	        if err != nil {
	            break
	        }
	    }
	    c.ws.Close()
	}
	
	var upgrader = &websocket.Upgrader{ReadBufferSize: 1024, WriteBufferSize: 1024}
	
	func wsHandler(w http.ResponseWriter, r *http.Request) {
	    ws, err := upgrader.Upgrade(w, r, nil)
	    if err != nil {
	        return
	    }
	    c := &connection{send: make(chan []byte, 256), ws: ws}
	    h.register <- c
	    defer func() { h.unregister <- c }()
	    go c.writer()
	    c.reader()
	}

wsHandler 方法被主函数当做[http handler](http://golang.org/pkg/net/http/#Handler)注册。HTTP 连接到 WebSocket 协议的升级，创建一个连接对象，注册这个连接到 sub ,并通过 [defer](http://weekly.golang.org/doc/effective_go.html#defer)延迟语句 来控制 连接的注销。

接着，wsHandler 方法开启 连接器的写入方法作为一个 goroutine。 写入方法将信息从连接器的 channel 转入 websocket。当 hub 关闭 channel 或者 在写入 websocket 时出错，写入方法关闭。

最后，wsHandler 方法 调用连接器的 读 方法。 读方法将 入站消息 从 websocket 转到 hub。

这里是服务器的代码的其余部分:

	package main

	import (
	    "flag"
	    "go/build"
	    "log"
	    "net/http"
	    "path/filepath"
	    "text/template"
	)
	
	var (
	    addr      = flag.String("addr", ":8080", "http service address")
	    assets    = flag.String("assets", defaultAssetPath(), "path to assets")
	    homeTempl *template.Template
	)
	
	func defaultAssetPath() string {
	    p, err := build.Default.Import("gary.burd.info/go-websocket-chat", "", build.FindOnly)
	    if err != nil {
	        return "."
	    }
	    return p.Dir
	}
	
	func homeHandler(c http.ResponseWriter, req *http.Request) {
	    homeTempl.Execute(c, req.Host)
	}
	
	func main() {
	    flag.Parse()
	    homeTempl = template.Must(template.ParseFiles(filepath.Join(*assets, "home.html")))
	    go h.run()
	    http.HandleFunc("/", homeHandler)
	    http.HandleFunc("/ws", wsHandler)
	    if err := http.ListenAndServe(*addr, nil); err != nil {
	        log.Fatal("ListenAndServe:", err)
	    }
	}

应用主程序启动 hub goroutine。 接着 主程序 注册 主页 和 websocket 连接器的控制器N。最后主程序启动 HTTP 服务器。

##Client 客户端

客户端的实现是一个简单的 HTML 文件：

	<html>
	<head>
	<title>Chat Example</title>
	<script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.4.2/jquery.min.js"></script>
	<script type="text/javascript">
	    $(function() {
	
	    var conn;
	    var msg = $("#msg");
	    var log = $("#log");
	
	    function appendLog(msg) {
	        var d = log[0]
	        var doScroll = d.scrollTop == d.scrollHeight - d.clientHeight;
	        msg.appendTo(log)
	        if (doScroll) {
	            d.scrollTop = d.scrollHeight - d.clientHeight;
	        }
	    }
	
	    $("#form").submit(function() {
	        if (!conn) {
	            return false;
	        }
	        if (!msg.val()) {
	            return false;
	        }
	        conn.send(msg.val());
	        msg.val("");
	        return false
	    });
	
	    if (window["WebSocket"]) {
	        conn = new WebSocket("ws://{{$}}/ws");
	        conn.onclose = function(evt) {
	            appendLog($("<div><b>Connection closed.</b></div>"))
	        }
	        conn.onmessage = function(evt) {
	            appendLog($("<div/>").text(evt.data))
	        }
	    } else {
	        appendLog($("<div><b>Your browser does not support WebSockets.</b></div>"))
	    }
	    });
	</script>
	<style type="text/css">
	html {
	    overflow: hidden;
	}
	
	body {
	    overflow: hidden;
	    padding: 0;
	    margin: 0;
	    width: 100%;
	    height: 100%;
	    background: gray;
	}
	
	#log {
	    background: white;
	    margin: 0;
	    padding: 0.5em 0.5em 0.5em 0.5em;
	    position: absolute;
	    top: 0.5em;
	    left: 0.5em;
	    right: 0.5em;
	    bottom: 3em;
	    overflow: auto;
	}
	
	#form {
	    padding: 0 0.5em 0 0.5em;
	    margin: 0;
	    position: absolute;
	    bottom: 1em;
	    left: 0px;
	    width: 100%;
	    overflow: hidden;
	}
	
	</style>
	</head>
	<body>
	<div id="log"></div>
	<form id="form">
	    <input type="submit" value="Send" />
	    <input type="text" id="msg" size="64"/>
	</form>
	</body>
	</html>

客户端使用 [jQuery](http://jquery.com/)

文档加载。脚本检查 websocket 的功能 。如果 WebSocket 功能 可以用，然后打开脚本与服务器的连接，并注册一个回调处理来自服务器的信息。回调使用 appendlog 方法将消息添加到聊天记录。

appendlog 方法检查在添加新的内容时的滚动位置，从而可以让用户手动滚动聊天记录而不会被新来的消息中断。如果聊天记录滚动至底部，那么新内容添加的到旧内容的后面。否则，滚动的位置不会改变。

表单处理器将用户的输入写入到 WebSocket 并且清除输入字段。


*参考：*[http://gary.burd.info/go-websocket-chat](http://gary.burd.info/go-websocket-chat)
