---
layout: post
title: NGINX 简明教程
date: 2017-05-21 00:22
author: admin
comments: true
categories: [NGINX]
tags: [NGINX,tutorial]
---

NGINX 是一款来自俄罗斯的HTTP 和反向代理（reverse proxy）服务器、邮件服务器，以及通用的 TCP/UDP 代理服务器，以其高性能被业界广泛采用。本文通过最简洁的方式，将 NGINX 核心应用做下介绍。

<!-- more -->

## 什么是 NGINX


NGINX是一个免费的、开源的、高性能的 HTTP 服务器和反向代理，以及一个  IMAP/POP3 代理服务器。 NGINX以其高性能、稳定性、丰富的功能集、简单的配置和低资源消耗而闻名。

NGINX 是为解决[C10K](http://www.kegel.com/c10k.html) 问题而编写的少数服务器之一。与传统服务器不同，NGINX 不依赖于线程来处理请求。相反，它使用更加可扩展的事件驱动（异步）架构。这种架构在负载下使用小的但更重要的是可预测的内存量。即使您不希望处理数千个并发请求，您仍然可以从 NGINX 的高性能和小内存中获益。 NGINX 在各个方向扩展：从最小的 VPS 一直到大型服务器集群。

## 控制语句


NGINX 启动后，有一个主进程（master process）和一个或多个工作进程（worker process），主进程的作用主要是读入和检查NGINX的配置信息，以及维护工作进程；工作进程才是真正处理客户端请求的进程。具体要启动多少个工作进程，可以在 NGINX 的配置文件`nginx.conf`中通过`worker_processes`指令指定。
可以通过以下这些命令来控制 NGINX：

```
nginx -s [ stop | quit | reopen | reload ]
```

其中：

* `nginx -s stop`： 强制停止NGINX，不管工作进程当前是否正在处理用户请求，都会立即退出。
* `nginx -s quit`：“优雅地”退出NGINX，执行这个命令后，工作进程会将当前正在处理的请求处理完毕后，再退出。
* `nginx -s reload`：重载配置信息。当NGINX的配置文件改变之后，同过执行这个命令，使更改的配置信息生效，而无需重新启动nginx.
* `nginx -s reopen`：重新打开日志文件。

## 配置服务器名称

服务器名称是用`server_name`指令来定义的，并且它决定了哪一个`server`块将用来处理给定的请求。可以使用精确名称、通配符、正则表达式来定义服务器名称。

```
server {
    listen       80;
    server_name  example.org  www.example.org;
    ...
}

server {
    listen       80;
    server_name  *.example.org;
    ...
}

server {
    listen       80;
    server_name  mail.*;
    ...
}

server {
    listen       80;
    server_name  ~^(?<user>.+)\.example\.net$;
    ...
}
```


当寻找一个虚拟服务器的名字，如果指定的名称匹配多个变体，例如，通配符和正则表达式都匹配，将会按照以下的顺序选择第一个匹配的变体：

* 精确名称
* 以星号（*）开头的最长的通配符，例如“*.example.org”
* 以星号（*）结尾的最长的通配符，例如“mail.*”
* 第一个匹配的正则表达式（根据在配置文件中出现的顺序）



## 配置 HTTPS 服务器

修改 `conf/nginx.conf` 文件，必须在配置文件 server 块中的监听指令 listen 后启用 ssl 参数，并且指定服务器证书 `ssl_certificate` 和私钥 `ssl_certificate_key` 的位置：

```
server {
    listen              443 ssl;
    server_name         www.example.com;
    ssl_certificate     www.example.com.crt;
    ssl_certificate_key www.example.com.key;
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers         HIGH:!aNULL:!MD5;
    ...
}
```


服务器证书是一个公共实体，它被发送给连接到服务器的每一个客户机。私钥是一个安全实体，应该存储在具有受限访问的文件中，但它必须可被nginx主进程读取。私钥也可以存储在与服务器证书相同的文件中：

```
    ssl_certificate     www.example.com.cert;
    ssl_certificate_key www.example.com.cert;
```

在这种情况下，这个证书文件的访问权限也应受到限制。虽然证书和密钥存储在一个文件中，但只有证书被发送到客户端。

指令 `ssl_protocols` 和 `ssl_ciphers` 可用于限制仅包括强版本和密码的 SSL/TLS 连接。 默认情况下，NGINX 使 用`ssl_protocols TLSv1 TLSv1.1 TLSv1.2`版本和`ssl_ciphers HIGH:!aNULL:!MD5`密码，因此通常不需要显式地配置它们。需要注意的是，这些指令的默认值在不同的版本里面已经变更好几次了。

## 作为 HTTP 负载均衡器

跨多个应用程序实例的负载均衡是优化资源利用率，最大限度地提高吞吐量，降低延迟，并确保容错配置一个常用的技术。

NGINX 支持如下负载均衡的机制（或方法）：

### 1. 轮询 

如果没有指定负载均衡的方法，那么 NGINX 默认采用的是轮询的方式。最简单的负载均衡配置如下：

```
http {
    upstream myapp1 {
        server srv1.example.com;
        server srv2.example.com;
        server srv3.example.com;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://myapp1;
        }
    }
}
```

3个同样实例的应用（srv1-srv3）是采用轮询方式。所有请求被代理到一组服务`myapp1`，同时，NGINX 运用 HTTP 负载均衡来分发请求。

反向代理被应用在 NGINX 内，包括负载均衡针对 HTTP、HTTPS、FASTCGI、uwsgi、SCGI 以及  memcached。

配置负载均衡针对 HTTPS 替代 HTTP 的话，仅仅使用 https 协议即可（proxy_pass https://myapp1）。

在为 FASTCGI、uwsgi、SCGI 或  memcached 设置负载均衡时，分别使用 fastcgi_pass、uwsgi_pass、scgi_pass 和  memcached_pass 指令。

### 2. 最少连接

在一些请求需要更长时间才能完成的情况下，最少连接可以更公正地控制应用程序实例的负载。

使用最少连接的负载平衡，NGINX 将不会加重一个有过多请求的应用服务负担，而是将它分发新的请求给最不繁忙的服务器。

在 NGINX 中需要通过设置`least_conn`来激活最少连接的负载均衡策略配置：

```
upstream myapp1 {
    least_conn;
    server srv1.example.com;
    server srv2.example.com;
    server srv3.example.com;
}
```

### 3. IP 哈希（会话持久）

注意，采用轮询或者最少连接的负载均衡策略，每个客户端的后续请求可能被分配带不同的服务器，不能保证同一个客户端总是指向同一个服务。如果需要告诉客户端分配到一个特定的应用服务，换句话，就是保持客户端的会话粘性（sticky）或者会话持久性（persitent），即总是尝试选着同一个特定的服务器，IP 哈希 负载均衡机制可以被使用。

采用 IP 哈希的策略，客户端的 IP 地址被用作一个哈希 key，决定哪个服务应该被选中来服务客户端的请求。这种方式，确保了同一个客户端来的请求将总是被指向同一个服务，除非这个服务不可用了。

配置IP 哈希负载均衡，只需要通过设置`ip_hash`来激活：

```
upstream myapp1 {
    ip_hash;
    server srv1.example.com;
    server srv2.example.com;
    server srv3.example.com;
}
```

### 权重（weight）

可以通过使用服务器的权重来影响 NGINX 的负载均衡算法，在上述轮询、最少请求、基于IP 哈希负载均衡配置中，服务器的权重没有配置，意味着所有服务器的权重都是一样的。特别是轮询，它意味着或多或少平等的分发请求到服务器（请求够多，并且请求以均匀方式进行处理，并完成够快）

当配置了一个 weight 变量到一个指定的服务后，权重被作为一个 NGINX 的负载均衡的决定的一部分：

```
upstream myapp1 {
    server srv1.example.com weight=3;
    server srv2.example.com;
    server srv3.example.com;
}
```

采用上面的配置，如果来了5个请求，3个到srv1，1个到srv2,1个到srv3。在最近的NGINX版本中，同样可以使用权重针对最少连接和IP 哈希的负载均衡策略。

### 健康监测

反向代理在 NGINX 中实现了被动的健康监测，如果响应从一个特定的服务器失败，携带着错误，NGINX 将标记这个服务器是失败的，并将尝试一段时间避免选择这个服务器作为后续请求的服务器。

`fail_timeout` 和  `max_fails` 用于设定指定时间内，应该发生连续不成功的数目。默认`max_fail`等于1，如果设置成0，相当于关闭这个服务器的健康监测。`fail_timeout`参数，定义多久服务器被标识失败。过了服务器`fail_timeout`失败超时间隔后，NGINX 将开始探测存活的客户端的请求，如果探测成功，服务被标识成存活状态。

## 压缩与解压

压缩响应通常会显着减少传输数据的大小。 然而，由于压缩在运行时发生，所以会增加处理开销，这可能会对性能产生负面影响。 

在向客户端发送响应之前，NGINX 会执行压缩，但不会“重复压缩”已经压缩过的响应。

### 启用压缩

要启用压缩，在 gzip 指令上请使用`on`参数:

```
gzip on;
```

默认情况下，NGINX 仅压缩使用MIME 类型 为 `text/html`的响应。要压缩其他 MIME 类型的响应，请包含`gzip_types`指令并列出相应的类型。

```
gzip_types text/plain application/xml;
```

要指定要压缩的响应的最小长度，请使用`gzip_min_length`指令。默认值为20字节，下面示例调整为1000：

```
gzip_min_length 1000;
```

默认情况下，NGINX 不会压缩对代理请求的响应（来自代理服务器的请求）。请求是否来自代理服务器是由请求中`Via`头字段的是否存来确定的。要配置这些响应的压缩，请使用`gzip_proxied`指令。该指令具有多个参数来指定 NGINX 应压缩哪种代理请求。例如，仅对不会在代理服务器上缓存的请求进行压缩响应，为此，`gzip_proxied`指令具有指示 NGINX 在响应中检查`Cache-Control`头字段的参数，如果值是 no-cache、no-store 或 private，则压缩响应。另外，您必须包括 expired 的参数来检查`Expires`头字段的值。这些参数在以下示例中与 auth 参数一起设置，该参数检查`Authorization`头字段的存在（授权响应特定于最终用户，并且通常不被缓存）：

```
gzip_proxied no-cache no-store private expired auth;
```

与大多数其他指令一样，配置压缩的指令可以包含在`http`上下文中，也可以包含在 `server` 或 `location` 块中。

gzip 压缩的整体配置可能如下所示。

```
server {
    gzip on;
    gzip_types      text/plain application/xml;
    gzip_proxied    no-cache no-store private expired auth;
    gzip_min_length 1000;
    ...
}
```

### 启用解压

某些客户端不支持使用 gzip 编码方法的响应。同时，可能需要存储压缩数据，或者即时压缩响应并将它们存储在缓存中。为了都能成功地服务于接受或者不接受压缩数据的客户端，针对后一种类型的客户端时，NGINX 可以在将数据发送时即时解压缩数据。

要启用运行时解压缩，请使用`gunzip`指令。

```
location /storage/ {
    gunzip on;
    ...
}
```


`gunzip`指令可以在与`gzip`指令相同的上下文中指定：

```
server {
    gzip on;
    gzip_min_length 1000;
    gunzip on;
    ...
}
```


请注意，此指令在单独的模块中定义（见`ngx_http_gunzip_module`<http://nginx.org/en/docs/http/ngx_http_gunzip_module.html>），默认情况下可能不包含在开源 NGINX 构建中。

### 发送压缩文件

要将文件的压缩版本发送到客户端而不是常规文件，请在适当的上下文中将`gzip_static`指令设置为 on。


```
location / {
    gzip_static on;
}
```


在这种情况下，为了服务`/path/to/file`的请求，NGINX 尝试查找并发送文件`/path/to/file.gz`。如果文件不存在，或客户端不支持 gzip，则 NGINX 将发送未压缩版本的文件。

请注意，`gzip_static`指令不启用即时压缩。它只是使用压缩工具预先压缩的文件。要在运行时压缩内容（而不仅仅是静态内容），请使用`gzip`指令。

该指令在单独的模块中定义（见`ngx_http_gzip_static_module`<http://nginx.org/en/docs/http/ngx_http_gzip_static_module.html>），默认情况下可能不包含在开源NGINX构建中。


## 参考文献

* 《NGINX 教程》:<https://github.com/waylau/nginx-tutorial>