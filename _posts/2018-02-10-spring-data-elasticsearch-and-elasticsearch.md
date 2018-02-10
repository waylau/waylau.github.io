---
layout: post
title: Spring Data Elasticsearch 与 Elasticsearch 的关系
date: 2018-02-10 00:22
author: admin
comments: true
categories: [Spring Data Elasticsearch,Elasticsearch]
tags: [Spring Data Elasticsearch,Elasticsearch]
---

Spring Data Elasticsearch 与 Elasticsearch 其实是两个不同的产品。本文带你简单的了解下，Spring Data Elasticsearch 与 Elasticsearch 的关系。


<!-- more -->
## Elasticsearch


Elasticsearch 是 NoSQL 之一，是用于构建大数据全文检索的利器。见课程（https://coding.imooc.com/class/125.html）。Elasticsearch  是采用 Java 编写的，提供丰富的 API 可供用户选择。

学习使用这些 API  是有学习成本的。要完全掌握整套 API 可能需要数月的时间。那么，怎么办呢?是否有速成的方式，来让用户快速上手呢？答案就是  Spring Data Elasticsearch。

## Spring Data Elasticsearch

如果你恰好是一个 Spring 的应用（就像 https://waylau.com/spring-boot-blog-video-release/ 或者 https://waylau.com/spring-cloud-video-release/），那么使用 Spring Data Elasticsearch 就是非常不错的选择。因为， Spring Data Elasticsearch  就是整个 Spring Data 家族的一员，有着与 Spring 良好的兼容。

Spring Data Elasticsearch 拥有与 Spring Data 家族其他成员（如  Spring Data JPA）通用的接口，这样，只要你学会了 Spring Data的接口，就可以胜任任何存储设备的使用，不管是关系型数据库MySQL、SQL Server、Oracle 还是NoSQL 譬如Elasticsearch 、MongoDB  等，都可以享用统一接口带来的便利，简直简单的不要不要哦~

以下就是一个例子：

```java
interface PersonRepository extends Repository<Person, Long> {
  List<Person> findByLastname(String lastname);
}
```

我们要先声明一个业务相关的接口 PersonRepository。 PersonRepository 继承自 Repository 即可。无需写具体的实现代码。

```java
class SomeClient {

  private final PersonRepository repository;

  SomeClient(PersonRepository repository) {
    this.repository = repository;
  }

  void doSomething() {
    List<Person> persons = repository.findByLastname("Lau");
  }
}
```

而后，注入这个接口即可使用了，而具体的实现 Spring Data Elasticsearch 会帮我们提供。是否很 Cool!

当然，这个 findByLastname 方法名称的定义还是有讲究的，要符合 Spring Data 的语意。这样 Spring Data 就会自动解析，猜测按照你定义的这个方法名，来生成相应的查询语句。从这个方法名字，我们一眼就能看出来，这个一个根据 Lastname 字段来查找 Person 的语句。

## 总结

任何技术都有优缺点，使用技术一定要符合自己业务的需要。使用 Spring Data Elasticsearch 还是 Elasticsearch 原生 API ，要自己去做评估。
 
Elasticsearch 原生 API 好处就是可以第一时间用到  Elasticsearch 的新特性。缺点的学习成本高。

用 Spring Data 的好处是，用统一的接口，适配所有不同的存储类型，如SQL、NoSQL 等等。缺点是，有时候适配的版本要比原生的 API 要慢。这个取决于 Spring Data Elasticsearch 团队的开发速度了。见“Spring Data Elasticsearch与Elasticsearch的版本关系”（https://www.imooc.com/article/19873）。


## 参考资料


* Spring Boot 教程：<https://github.com/waylau/spring-boot-tutorial>
* Spring Cloud 教程：<https://github.com/waylau/spring-cloud-tutorial>
* https://waylau.com/spring-boot-blog-video-release/
* https://waylau.com/spring-cloud-video-release/
* https://www.imooc.com/article/19873



