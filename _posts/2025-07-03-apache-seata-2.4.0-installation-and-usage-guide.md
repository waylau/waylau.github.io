---
layout: post
title: Apache Seata 2.4.0安装与使用指南
date: 2025-07-03 00:22
author: admin
comments: true
categories: [Seata]
tags: [Seata]
---

本文主要介绍在Windows系统下通过ZIP文件安装Apache Seata 2.4.0。

<!-- more -->

## 下载安装包

从Seata官方（<https://seata.apache.org/zh-cn/download/seata-server/>）下载2.4.0版本编译文件压缩包（如`apache-seata-2.4.0-incubating-bin.tar.gz`）。


解压至目标目录（如`D:\dev\java\apache-seata-2.4.0-incubating-bin`）。其中，该目录下有两个目录：

* seata-server：Seata Server端
* seata-namingserver：Seata原生的注册中心

## 配置和启动Seata Server端


### 配置Seata Server端

Seata Server端存储模式（store.mode）现有file、db、redis、raft等。file模式是默认的，无需改动配置，直接启动Seata Server即可。也可以按需修改配置文件`conf/application.yml`，比如修改日志目录：



```yml
server:
  port: 7091

spring:
  application:
    name: seata-server

logging:
  config: classpath:logback-spring.xml
  file:
    path: D:\\data\\seata\\logs     ## seata-server的日志目录
  extend:
    logstash-appender:
      # off by default
      enabled: false
      destination: 127.0.0.1:4560
    kafka-appender:
      # off by default
      enabled: false
      bootstrap-servers: 127.0.0.1:9092
      topic: logback_to_logstash
      producer:
        acks: 0
        linger-ms: 1000
        max-block-ms: 0
    metric-appender:
      # off by default
      enabled: false

seata:
  config:
    # support: nacos, consul, apollo, zk, etcd3
    type: file
  registry:
    # support: nacos, eureka, redis, zk, consul, etcd3, sofa
    type: file
  store:
    # support: file 、 db 、 redis 、 raft
    mode: file
  #  server:
  #    service-port: 8091 #If not configured, the default is '${server.port} + 1000'
```

### 启动Seata Server端


在Windows下，执行如下命令启动：

```cmd
bin\seata-server.bat
```

### 验证Seata Server端


看到如下日志，则证明启动成功：

```
17:25:55.616  INFO --- [                     main] [a.server.session.SessionHolder] [                init]  [] : use session store mode: file
17:25:55.910  INFO --- [                     main] [rpc.netty.NettyServerBootstrap] [               start]  [] : Server started, service listen port: 8091
17:25:55.942  INFO --- [                     main] [ache.seata.server.ServerRunner] [                 run]  [] :
 you can visit seata console UI on namingserver.
 log path: D:\\data\\seata\\logs.
17:25:55.944  INFO --- [                     main] [ache.seata.server.ServerRunner] [                 run]  [] : seata server started in 939 millSeconds
```



## 配置和启动Seata原生的注册中心（可选）


Seata 2.4开始控制台从Seata Server迁移至Seata原生的注册中心，故如果想要访问控制台，还需要启动Seata原生的注册中心。


### 配置Seata原生的注册中心

Seata原生的注册中心按需修改配置文件`conf/application.yml`，比如修改端口、日志目录：



```yml
server:
  port: 8011 ## namingserver的端口

spring:
  application:
    name: seata-namingserver
logging:
  config: classpath:logback-spring.xml
  file:
    path: D:\\data\\seata\\logs ## namingserver的日志目录
console: ## 2.4开始控制台从Seata-server迁移至Namingserver，故此配置为控制台相关配置
  user:
    username: seata ## 控制台界面，和open-api鉴权所需账号，强烈建议修改不要采用默认值
    password: seata ## 控制台界面，和open-api鉴权所需密码，强烈建议修改不要采用默认值
heartbeat:
  threshold: 90000  ## 当Seata-server节点非优雅下线时，摘除时间
  period: 60000 ## 每60秒检查节点心跳是否正常，如果超过threshold的配置，将进行摘除
seata:
  security:
    secretKey: SeataSecretKey0c382ef121d778043159209298fd40bf3850a017 ## 签发token秘钥，强烈建议修改，不要采用默认值
    tokenValidityInMilliseconds: 1800000 ## token过期时间
    csrf-ignore-urls: /naming/v1/**,/api/v1/naming/** ## 无需csrf防护接口，采用默认值即可，因为此默认值是提供给client的open-api，不通过控制台。
    ignore:
      urls: /,/**/*.css,/**/*.js,/**/*.html,/**/*.map,/**/*.svg,/**/*.png,/**/*.jpeg,/**/*.ico,/api/v1/auth/login,/version.json,/naming/v1/health,/error
```

### 启动Seata原生的注册中心


在Windows下，执行如下命令启动：

```cmd
bin\seata-namingserver.bat
```

### 验证Seata原生的注册中心


看到如下日志，则证明启动成功：

```
17:52:01.293  INFO --- [                     main] [oyote.http11.Http11NioProtocol] [                 log]  [] : Starting ProtocolHandler ["http-nio-8011"]
17:52:01.327  INFO --- [                     main] [mbedded.tomcat.TomcatWebServer] [               start]  [] : Tomcat started on port(s): 8011 (http) with context path ''
17:52:01.351  INFO --- [                     main] [server.NamingserverApplication] [          logStarted]  [] : Started NamingserverApplication in 9.185 seconds (JVM running for 20.985)
```


## Seata Server端使用Seata原生的注册中心（可选）

修改配置文件`conf/application.yml`的注册中心配置：



```yml
server:
  port: 7091

spring:
  application:
    name: seata-server

logging:
  config: classpath:logback-spring.xml
  file:
    path: D:\\data\\seata\\logs     ## seata-server的日志目录
  extend:
    logstash-appender:
      # off by default
      enabled: false
      destination: 127.0.0.1:4560
    kafka-appender:
      # off by default
      enabled: false
      bootstrap-servers: 127.0.0.1:9092
      topic: logback_to_logstash
      producer:
        acks: 0
        linger-ms: 1000
        max-block-ms: 0
    metric-appender:
      # off by default
      enabled: false

seata:
  config:
    # support: nacos, consul, apollo, zk, etcd3
    type: file
  registry:
    # support: nacos, eureka, redis, zk, consul, etcd3, sofa
    type: seata
    seata:
      server-addr: 127.0.0.1:8011   ##上一步配置的namingserver的ip+端口，若有多个namingserver节点则用逗号分割
      cluster: default  ##集群名称
      namespace: public  ##命名空间
      heartbeat-period: 5000  ##心跳时间
      username: seata
      password: seata
  store:
    # support: file 、 db 、 redis 、 raft
    mode: file
  #  server:
  #    service-port: 8091 #If not configured, the default is '${server.port} + 1000'
```



重新启动Seata Server端，访问Seata原生的注册中心控制台<http://localhost:8011/naming/v1/health>，不过并未显示UI界面，只是提示“Whitelabel Error Page”，查看源码疑似功能并未开发完成。访问接口<http://localhost:8011/naming/v1/health>，返回以下内容：

```json
{"code":"200","message":"success","success":true}
```




## 参考引用



* https://seata.apache.org/zh-cn/docs/ops/deploy-guide-beginner
* https://seata.apache.org/zh-cn/docs/ops/deploy-server
* 