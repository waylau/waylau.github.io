---
layout: post
title: MyBatis四大核心概念
date: 2018-06-22 00:22
author: admin
comments: true
categories: [MyBatis]
tags: [MyBatis]
---

本文讲解 MyBatis 四大核心概念（SqlSessionFactoryBuilder、SqlSessionFactory、SqlSession、Mapper）及其生命周期。

<!-- more -->

MyBatis 作为互联网数据库映射工具界的“上古神器”，训有四大“神兽”，谓之：SqlSessionFactoryBuilder、SqlSessionFactory、SqlSession、Mapper。可以说，了解了这四大核心，便可知 MyBatis 八九。

## SqlSessionFactoryBuilder

从命名上可以看出，这个是一个 Builder 模式的，用于创建 SqlSessionFactory 的类。SqlSessionFactoryBuilder 根据配置来构造 SqlSessionFactory。

其中配置方式有两种

### 1. XML 文件方式

XML 文件方式是作为常用的一种方式：

```java
String resource = "org/mybatis/example/mybatis-config.xml";

InputStream inputStream = Resources.getResourceAsStream(resource);

SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```

mybatis-config.xml 就是我们的配置文件:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="${driver}"/>
            <property name="url" value="${url}"/>
            <property name="username" value="${username}"/>
            <property name="password" value="${password}"/>
        </dataSource>
        </environment>
    </environments>
    <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
    </mappers>
</configuration>
```


### 2. Java Config

这是第二种配置方式，通过 Java 代码来配置：

```java
DataSource dataSource = BlogDataSourceFactory.getBlogDataSource();

TransactionFactory transactionFactory = new JdbcTransactionFactory();

Environment environment = new Environment("development", transactionFactory, dataSource);

Configuration configuration = new Configuration(environment);
configuration.addMapper(BlogMapper.class);

SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(configuration);
```


Java Config 相比较 XML 文件的方式而言，会有一些限制。比如修改了配置文件需要重新编译，注解方式没有 XML 配置项多等。所以，业界大多数情况下是选择 XML 文件的方式。但到底选择哪种方式，这个要取决与自己团队的需要。比如，项目的 SQL 语句不复杂，也不需要一些高级的 SQL 特性，那么 Java Config 则会更加简洁一点；反之，则可以选择 XML 文件的方式。 

## SqlSessionFactory

SqlSessionFactory 顾名思义，是用于生产 SqlSession 的工厂。

通过如下的方式来获取 SqlSession 实例：

```java
SqlSession session = sqlSessionFactory.openSession();
```

## SqlSession

SqlSession 包含了执行 SQL 的所有的方法。以下是示例：

```java
SqlSession session = sqlSessionFactory.openSession();
try {
Blog blog = session.selectOne(
"org.mybatis.example.BlogMapper.selectBlog", 101);
} finally {
session.close();
}
```

当然，下面的方式可以做到类型安全：

```java
SqlSession session = sqlSessionFactory.openSession();
try {
BlogMapper mapper = session.getMapper(BlogMapper.class);
Blog blog = mapper.selectBlog(101);
} finally {
session.close();
}
```


## Mapper


Mapper 顾名思义，是用做 Java 与 SQL 之间的映射的。包括了 Java 映射为 SQL 语句，以及 SQL 返回结果映射为 Java。

比如，下面是一个常见的 Mapper 接口映射文件：


```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.example.BlogMapper">
<select id="selectBlog" resultType="Blog">
select * from Blog where id = #{id}
</select>
</mapper>
```


其中 "org.mybatis.example.BlogMapper" 就是我们要射射的接口，selectBlog 就是BlogMapper上的方法。而这个 selectBlog 具体就是要执行“select * from Blog where id = #{id}”这个 SQL 语句。

这样，我们就能通过

```java
Blog blog = session.selectOne(
"org.mybatis.example.BlogMapper.selectBlog", 101);
```

或者是

```java
BlogMapper mapper = session.getMapper(BlogMapper.class);
Blog blog = mapper.selectBlog(101);
```

来获取到执行的结果。


当然，如果是采用注解的方式的话，可以省去 XML 文件：

```java
public interface BlogMapper {
@Select("SELECT * FROM blog WHERE id = #{id}")
Blog selectBlog(int id);
}
```