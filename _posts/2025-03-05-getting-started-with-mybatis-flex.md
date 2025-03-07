---
layout: post
title: MyBatis-Flex快速入门使用Spring Boot及H2
date: 2025-03-05 00:22
author: admin
comments: true
categories: [MyBatis-Flex]
tags: [MyBatis-Flex]
---


本节主要介绍如何通过快速开启一个MyBatis-Flex应用。

<!-- more -->


## MyBatis-Flex 简介

MyBatis-Flex 是一个优雅的 MyBatis 增强框架，它非常轻量、同时拥有极高的性能与灵活性。可以轻松的使用 Mybaits-Flex 链接任何数据库，其内置的 QueryWrapper 帮助开发人员极大的减少了 SQL 编写的工作的同时，减少出错的可能性。

总而言之，MyBatis-Flex 能够极大地提高开发人员的开发效率和开发体验，让开发人员有更多的时间专注于自己的事情。


MyBatis-Flex 特征如下。

#### 1. 很轻量
> MyBatis-Flex 整个框架只依赖 MyBatis，再无其他任何第三方依赖。

#### 2. 只增强
> MyBatis-Flex  支持 CRUD、分页查询、多表查询、批量操作，但不丢失 MyBatis 原有的任何功能。

#### 3. 高性能
> MyBatis-Flex 采用独特的技术架构、相比许多同类框架，MyBatis-Flex 的在增删改查等方面的性能均超越其 5-10 倍或以上。

#### 4. 更灵动
> MyBatis-Flex 支持多主键、多表查询、逻辑删除、乐观锁、数据脱敏、数据加密、多数据源、分库分表、字段权限、字段加密、多租户、事务管理、SQL 审计等特性。 这一切，免费且灵动。



## 创建 Spring Boot 项目

当前使用的是 SpringBoot v3.x 版本，需要添加 mybatis-flex-spring-boot3-starter 依赖。同时，为了便于测试，也引入了 HikariCP 数据源管理及 H2 数据库如下。代码所示：


```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>3.4.3</version>
  <relativePath/>
</parent>

<properties>
		<java.version>17</java.version>
		<mybatis-flex.version>1.10.8</mybatis-flex.version>
	</properties>
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
  </dependency>

  <dependency>
    <groupId>com.zaxxer</groupId>
    <artifactId>HikariCP</artifactId>
  </dependency>

  <dependency>
    <groupId>com.mybatis-flex</groupId>
    <artifactId>mybatis-flex-spring-boot3-starter</artifactId>
    <version>${mybatis-flex.version}</version>
  </dependency>
  <dependency>
    <groupId>com.mybatis-flex</groupId>
    <artifactId>mybatis-flex-processor</artifactId>
    <version>${mybatis-flex.version}</version>
    <scope>provided</scope>
  </dependency>
  <dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
  </dependency>
</dependencies>
```

应用配置application.yml内容如下：

```
spring:
  datasource:
    driver-class-name: org.h2.Driver
    url: jdbc:h2:mem:test;DB_CLOSE_DELAY=-1
    username: sa
    password:
  sql:
    init:
      schema-locations: classpath:schema.sql
      data-locations: classpath:data.sql
```



MybatisFlexH2Application.java代码如下：


```java
package com.waylau.mybatisflex.mybatisflexh2;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan("com.waylau.mybatisflex.mybatisflexh2.mapper")
public class MybatisFlexH2Application {

	public static void main(String[] args) {
		SpringApplication.run(MybatisFlexH2Application.class, args);
	}

}
```

`@MapperScan`用于配置扫描Mapper所在的目录。

## 初始化数据库

Spring Boot 针对 JDBC DataSource 可以使用 DML 脚本自动创建库表，并使用DDL脚本初始化其数据。默认情况下，从`classpath*：schema.sql`加载模式脚本，从`classpath*：data.sql`加载数据脚本。我们在resources目录下的创建schema.sql和data.sql文件，内容如下。

#### 1. schema.sql

```
create table t_role (id int, role_name varchar);
```

#### 2. data.sql

```
INSERT INTO t_role (id, role_name) VALUES (1, '骑士');
INSERT INTO t_role (id, role_name) VALUES (2, '法师');
INSERT INTO t_role (id, role_name) VALUES (3, '机械师');
```



## 编写 MyBatis-Flex 代码

#### 1. 定义实体


```java
package com.waylau.mybatisflex.mybatisflexh2.model;

import com.mybatisflex.annotation.Id;
import com.mybatisflex.annotation.KeyType;
import com.mybatisflex.annotation.Table;

@Table("t_role")
public class Role {

    @Id(keyType = KeyType.Auto)
    private Long id;

    private String roleName;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getRoleName() {
        return roleName;
    }

    public void setRoleName(String roleName) {
        this.roleName = roleName;
    }

}
```

#### 2. 定义Mapper


```
package com.waylau.mybatisflex.mybatisflexh2.mapper;


import com.mybatisflex.core.BaseMapper;
import com.waylau.mybatisflex.mybatisflexh2.model.Role;

public interface RoleMapper extends BaseMapper<Role> {
}
```


## 编写测试代码


MybatisFlexH2ApplicationTests.java 为测试类，测试代码如下：


```java
package com.waylau.mybatisflex.mybatisflexh2;

import com.mybatisflex.core.query.QueryWrapper;
import com.waylau.mybatisflex.mybatisflexh2.mapper.RoleMapper;
import com.waylau.mybatisflex.mybatisflexh2.model.Role;
import org.junit.jupiter.api.Test;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.List;

import static com.waylau.mybatisflex.mybatisflexh2.model.table.RoleTableDef.ROLE;

@SpringBootTest
class MybatisFlexH2ApplicationTests {

    private static Logger log = LoggerFactory.getLogger(MybatisFlexH2ApplicationTests.class);

    @Autowired
    private RoleMapper roleMapper;

    @Test
    void contextLoads() {
        // 查询角色中带“师”的角色列表 
        QueryWrapper queryWrapper = QueryWrapper.create()
                .select()
                .where(ROLE.ROLE_NAME.like("师"));

        List<Role> roleList = roleMapper.selectListByQuery(queryWrapper);

        roleList.forEach(role -> log.info("role id {}, {}", role.getId(), role.getRoleName()));
    }
}
```


运行应用，控制台输入日志内容如下：

```
2025-03-05T09:12:54.511+08:00  INFO 18220 --- [           main] c.w.m.m.MybatisFlexH2ApplicationTests    : role id 2, 法师
2025-03-05T09:12:54.511+08:00  INFO 18220 --- [           main] c.w.m.m.MybatisFlexH2ApplicationTests    : role id 3, 机械师
```

## 参考引用

* 原文同步至：<https://waylau.com/getting-started-with-mybatis-flex/>

更多示例源码、Mybatis-Flex学习资料可见

* 《跟老卫学Mybatis-Flex开发》 开源免费教程，<https://github.com/waylau/mybatis-flex-tutorial>
