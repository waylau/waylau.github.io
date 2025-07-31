---
layout: post
title: Spring Cloud Gateway Server Web MVC报错“Unsupported transfer encoding chunked”解决
date: 2025-07-30 00:22
author: admin
comments: true
categories: [Spring Cloud]
tags: [Spring Cloud]
---

本文主要介绍在Spring Cloud Gateway Server Web MVC中报错“Unsupported transfer encoding: chunked”问题的原因，及解决方案。

<!-- more -->

`org.apache.hc.core5.http.NotImplementedException: Unsupported Unsupported transfer encoding: chunked` 错误通常是由于 **Feign 依赖的 HTTP 客户端与服务端使用的 `chunked` 传输编码不兼容** 导致的。具体原因和解决方案如下：


## 原因分析


基于Spring Cloud Gateway Server Web MVC的API网关问题控制台输出报错信息如下：


```
org.apache.hc.core5.http.NotImplementedException: Unsupported transfer encoding: chunked
	at org.apache.hc.core5.http.impl.DefaultContentLengthStrategy.determineLength(DefaultContentLengthStrategy.java:90) ~[httpcore5-5.3.4.jar:5.3.4]
	at org.apache.hc.core5.http.impl.io.DefaultBHttpClientConnection.receiveResponseEntity(DefaultBHttpClientConnection.java:355) ~[httpcore5-5.3.4.jar:5.3.4]
	at org.apache.hc.core5.http.impl.io.HttpRequestExecutor.execute(HttpRequestExecutor.java:213) ~[httpcore5-5.3.4.jar:5.3.4]
	at org.apache.hc.client5.http.impl.classic.InternalExecRuntime.lambda$execute$0(InternalExecRuntime.java:236) ~[httpclient5-5.4.4.jar:5.4.4]
	at org.apache.hc.client5.http.impl.io.PoolingHttpClientConnectionManager$InternalConnectionEndpoint.execute(PoolingHttpClientConnectionManager.java:791) ~[httpclient5-5.4.4.jar:5.4.4]
	at org.apache.hc.client5.http.impl.classic.InternalExecRuntime.execute(InternalExecRuntime.java:233) ~[httpclient5-5.4.4.jar:5.4.4]
```

虽然错误是报在API网关，但实际是需要在微服务中去解决。问题原因如下：



HTTP 中的 `chunked` 传输编码用于动态分块传输数据（适用于数据大小不确定的场景）。该错误表明：
- 服务端返回的响应使用了 `Transfer-Encoding: chunked` 编码。
- Feign 客户端使用的 HTTP 客户端（如 Apache HttpClient 5.x）不支持或未正确配置 `chunked` 编码处理。


## 解决方案

REST接口类型尽量采用具体类型，避免 Feign 客户端调用过程中导致推导类型失真。

例如：

```java
@FeignClient(
        name = "rednote-content-microservice",
        fallback = ContentServiceClientFallback.class // 指定降级实现类
)
public interface ContentServiceClient {
    @GetMapping("/note/user/{userId}")
    ResponseEntity<?> getNotesWithUser(
            @PathVariable Long userId,
            @RequestParam(defaultValue = "1") int page,
            @RequestParam(defaultValue = "12") int size);
}
```

改为：

```java
@FeignClient(
        name = "rednote-content-microservice",
        fallback = ContentServiceClientFallback.class // 指定降级实现类
)
public interface ContentServiceClient {
    @GetMapping("/note/user/{userId}")
    ResponseEntity<NotesWithUserDto> getNotesWithUser(
            @PathVariable Long userId,
            @RequestParam(defaultValue = "1") int page,
            @RequestParam(defaultValue = "12") int size);
}
```

```java
@GetMapping("/user/{userId}")
public ResponseEntity<?> getNotesWithUser(
        @PathVariable Long userId,
        @RequestParam(defaultValue = "1") int page,
        @RequestParam(defaultValue = "12") int size) {
    ResponseEntity<UserDto> response = userServiceClient.findByUserId(userId);
    UserDto user = response.getBody();

    // 获取用户笔记列表（分页）
    Page<Note> notePage = noteService.getNotesByUser(userId, page - 1, size);

    // 转换为 DTO
    List<NoteExploreDto> noteExploreDtoList = notePage.map(note -> noteService.toExploreDto(note, user)).getContent();

    Map<String, Object> map = new HashMap<>();
    map.put("user", user);
    map.put("noteList", noteExploreDtoList);
    map.put("currentPage", page);
    map.put("totalPages", notePage.getTotalPages());

    return ResponseEntity.ok()
            .body(map);
}
```


改为：


```java
@GetMapping("/user/{userId}")
public ResponseEntity<?> getNotesWithUser(
        @PathVariable Long userId,
        @RequestParam(defaultValue = "1") int page,
        @RequestParam(defaultValue = "12") int size) {
    ResponseEntity<UserDto> response = userServiceClient.findByUserId(userId);
    UserDto user = response.getBody();

    // 获取用户笔记列表（分页）
    Page<Note> notePage = noteService.getNotesByUser(userId, page - 1, size);

    // 转换为 DTO
    List<NoteExploreDto> noteExploreDtoList = notePage.map(note -> noteService.toExploreDto(note, user)).getContent();

    NotesWithUserDto dto = new NotesWithUserDto(user, noteExploreDtoList, page, notePage.getTotalPages());
    ResponseEntity<NotesWithUserDto> responseEntity = ResponseEntity.ok()
            .body(dto);
    return responseEntity;
}
```


## 参考引用



* 本文同步至：<https://waylau.com/spring-cloud-gateway-server-webmvc-unsupported-transfer-encoding-chunked>
* 源码见《跟老卫学Spring Cloud开发》开源免费教程， <https://github.com/waylau/spring-cloud-tutorial/>
* 《Spring Boot 企业级应用开发实战》（北京大学出版社）
* 《Spring Cloud 微服务架构开发实战》（北京大学出版社）
* 《Spring 5 开发大全》（北京大学出版社）
