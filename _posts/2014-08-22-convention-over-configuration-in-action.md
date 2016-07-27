---
layout: post
title: 约定大于配置--实战
date: 2014-08-22 01:40
author: admin
comments: true
categories: [Architecture]
tags: [Architecture]
---
 
约定优于配置是一个简单的概念。 系统，类库，框架应该假定合理的默认值，而非要求提供不必要的配置。 在大部分情况下，你会发现使用框架提供的默认值会让你的项目运行的更快。

零配置并不是完全没有配置，而是通过约定来减少配置, 减少 XML 

#约定代码结构或命名规范来减少配置数量

如果模型中有个名为Sale的类，那么数据库中对应的表就会默认命名为sale。只有在偏离这一约定时，例如将该表命名为"products_sold"，才需写有关这个名字的配置。

比如 EJB3 持久化，将一个特殊的Bean持久化，你所需要做的只是将这个类标注为 @Entity 。 框架将会假定表名和列名是基于类名和属性名。 系统也提供了一些钩子，当有需要的时候你可以重写这些名字.

比如 maven 项目约定，在没有自定义的情况下，源代码假定是在 `/workspace/content-zh/src/main/java`，资源文件假定是在`/workspace/content-zh/src/main/resources`
。测试代码假定是在 `/workspace/content-zh/src/test`。项目假定会产生一个 JAR 文件。Maven假定你想要把编译好的字节码放到 `/workspace/content-zh/target/classes` 并且在 `/workspace/content-zh/target` 创建一个可分发的 JAR 文件。Maven 对约定优于配置的应用不仅仅是简单的目录位置，Maven 的核心插件使用了一组通用的约定，以用来编译源代码，打包可分发的构件，生成 web 站点，还有许多其他的过程。 Maven 的力量来自它的"武断"，它有一个定义好的生命周期和一组知道如何构建和装配软件的通用插件。如果你遵循这些约定，Maven 只需要几乎为零的工作——仅仅是将你的源代码放到正确的目录，Maven 将会帮你处理剩下的事情。

比如 Yeoman 创建项目，只需一行代码

	yeoman init angular 

就会创建整个详细结构包括渲染路由的骨架，单元测试等

比如 [HTML5 Boilerplate](http://html5boilerplate.com/),为App的默认模板以及文件路径规范，无论是网站或者富UI的App，都可以采用这个模板作为起步。HTML5 Boilerplate的模板核心部分不过30行，但是每一行都可谓千锤百炼，可以用最小的消耗解决一些前端的顽固问题：
![Boilerplate](http://c.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=3d7cf70fcbfcc3ceb0c0c936a27ea7b5/b812c8fcc3cec3fd866fdc58d588d43f8794274d.jpg?referer=f86e14b2a70f4bfbd5c7ab648143&x=.jpg)

#采用更简洁的配置方式来替代XML

##比如：`hibernate.properties`的 c3p0 方式:

	hibernate.connection.driver_class = org.postgresql.Driver
	hibernate.connection.url = jdbc:postgresql://localhost/mydatabase
	hibernate.connection.username = myuser
	hibernate.connection.password = secret
	hibernate.c3p0.min_size=5
	hibernate.c3p0.max_size=20
	hibernate.c3p0.timeout=1800
	hibernate.c3p0.max_statements=50
	hibernate.dialect = org.hibernate.dialect.PostgreSQL82Dialect

##比如：`hibernate.properties` 的 JNDI 方式： 

	hibernate.connection.datasource = java:/comp/env/jdbc/test
	hibernate.transaction.factory_class = \org.hibernate.transaction.JTATransactionFactory
	hibernate.transaction.manager_lookup_class = \ org.hibernate.transaction.JBossTransactionManagerLookup
	hibernate.dialect = org.hibernate.dialect.PostgreSQL82Dialect

##比如 Apache Shiro 的 ini 配置

	[users]
	root = secret, admin
	guest = guest, guest
	presidentskroob = 12345, president
	darkhelmet = ludicrousspeed, darklord, schwartz
	lonestarr = vespa, goodguy, schwartz
	
	[roles]
	admin = *
	schwartz = lightsaber:*
	goodguy = winnebago:drive:eagle5


##Hibernate通过代码配置

	Configuration cfg = new Configuration()
	    .addClass(org.hibernate.auction.Item.class)
	    .addClass(org.hibernate.auction.Bid.class)
	    .setProperty("hibernate.dialect", "org.hibernate.dialect.MySQLInnoDBDialect")
	    .setProperty("hibernate.connection.datasource", "java:comp/env/jdbc/test")
	    .setProperty("hibernate.order_updates", "true");


##Gradle 替代 Maven
采用 Maven 

	<dependency>
	   <groupId>org.hibernate</groupId>
	   <artifactId>hibernate-core</artifactId>
	   <version>4.3.6.Final</version>
	</dependency>

采用 Gradle 只需一行

	org.hibernate:hibernate-core:4.3.6.Final


#通过注解约定其含义来减少配置数量

##spring注解
Spring会自动搜索某些路径下的Java类，并将这些java类注册为Bean实例，这样就省去了将所有 bean 都在 XML 配置。

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xmlns:context="http://www.springframework.org/schema/context"
		xsi:schemaLocation="http://www.springframework.org/schema/beans
			http://www.springframework.org/schema/beans/spring-beans.xsd
			http://www.springframework.org/schema/context
			http://www.springframework.org/schema/context/spring-context.xsd">

		<context:component-scan base-package="com.waylau.rest"/>

	</beans>

注意：如果配置了`<context:component-scan/>`那么`<context:annotation-config/>`标签就可以不用在 xml 中配置了，因为前者包含了后者。另外`<context:component-scan/>`还提供了两个子标签`<context:include-filter>` `<context:exclude-filter>`用来控制扫描文件的颗粒度,例如

	<beans>
		<context:component-scan base-package="com.waylau.rest">
		<context:include-filter type="regex" expression=".*Stub.*Repository"/>
		<context:exclude-filter type="annotation"expression="org.springframework.stereotype.Repository"/>
		</context:component-scan>
	</beans>

代码实现

	public static void main(String[] args) {
		AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
		ctx.scan("com.waylau.rest");
		ctx.refresh();
		MyService myService = ctx.getBean(MyService.class);
	}

Spring会合适的将显示指定路径下的类全部注册成Spring Bean。 Spring通过使用特殊的Annotation来标注Bean类。

* @Component :标注一个普通的Spring Bean类。
* @Controller : 标注一个控制器组件类。
* @Service: 标注一个业务逻辑组件类。
* @Repository : 标注一个DAO组件类。
 


##Hibernate注解
表与实体映射

	@Entity
	@Table(name="TBL_FLIGHT", 
	       schema="AIR_COMMAND", 
	       uniqueConstraints=
	           @UniqueConstraint(
	               name="flight_number", 
	               columnNames={"comp_prefix", "flight_number"} ) )
	public class Flight implements Serializable {
	    @Column(name="comp_prefix")
	    public String getCompagnyPrefix() { return companyPrefix; }
	
	    @Column(name="flight_number")
	    public String getNumber() { return number; }
	}

甚至 SQL 也可以注解

	@Entity
	@Table(name="CHAOS")
	@SQLInsert( sql="INSERT INTO CHAOS(size, name, nickname, id) VALUES(?,upper(?),?,?)")
	@SQLUpdate( sql="UPDATE CHAOS SET size = ?, name = upper(?), nickname = ? WHERE id = ?")
	@SQLDelete( sql="DELETE CHAOS WHERE id = ?")
	@SQLDeleteAll( sql="DELETE CHAOS")
	@Loader(namedQuery = "chaos")
	@NamedNativeQuery(name="chaos", query="select id, size, name, lower( nickname ) as nickname from CHAOS where xml:id= ?", resultClass = Chaos.class)
	public class Chaos {
	    @Id
	    private Long id;
	    private Long size;
	    private String name;
	    private String nickname;
		//...
	}
##Jersey 2.X 实现 REST 和 MVC

	@POST
	@Produces({"text/html”}) 
	@Consumes(MediaType.APPLICATION_FORM_URLENCODED) 
	@Template(name = "/short-link”) @ErrorTemplate(name = "/error-form") 
	@Valid
	public ShortenedLink createLink(@NotEmpty @FormParam("link") final String link) { 
	    // ... 
	}

##Shiro 的注解

没有注解的情况

	//get the current Subject
	Subject currentUser =
	    SecurityUtils.getSubject();
	
	if (currentUser.hasRole(“administrator”)) {
	   //do something in here that only a administrator‏
	} else {
	    //don’t  do ‏
	}

用了注解，简洁很多

	@RequiresRoles( “administrator” )
	public void openAccount( Account acct ) { 
	    //do something in here that only a administrator
	}
##Struts 2.x 注解

	package com.waylau.actions;
	 
	import com.opensymphony.xwork2.ActionSupport;
	import org.apache.struts2.convention.annotation.Action;
	import org.apache.struts2.convention.annotation.Actions;
	import org.apache.struts2.convention.annotation.Result;
	import org.apache.struts2.convention.annotation.Results;
	 
	@Results({
	  @Result(name="failure", location="fail.jsp")
	})
	public class HelloWorld extends ActionSupport {
	  @Action(value="/different/url",
	    results={@Result(name="success", location="http://struts.apache.org", type="redirect")}
	  )
	  public String execute() {
	    return SUCCESS;
	  }
	 
	  @Action("/another/url")
	 
	  public String doSomething() {
	    return SUCCESS;
	  }
	}

#参考

* [Spring](http://docs.spring.io/spring/docs/4.0.6.RELEASE/spring-framework-reference/htmlsingle/)
* [Hibernate](http://docs.jboss.org/hibernate/orm/4.3/manual/en-US/html/)
* [Jersey](https://jersey.java.net/documentation/latest/user-guide.html)
* [Apache Shiro](http://shiro.apache.org/java-authorization-guide.html)
* [Apache Struts](http://struts.apache.org/release/2.3.x/docs/convention-plugin.html)
* [http://www.waylau.com/build-java-project-with-maven/](http://www.waylau.com/build-java-project-with-maven/)
* [https://github.com/waylau/Gradle-2-User-Guide](https://github.com/waylau/Gradle-2-User-Guide)