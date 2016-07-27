---
layout: post
title: 用Jersey构建RESTful服务6--Jersey+SQLServer+Hibernate4.3
date: 2014-03-26 03:24
author: admin
comments: true
categories: [Java, Jersey, REST]
tags: [Java, Jersey, RESTful]
---
#一、总体说明

本例运行演示了用Jersey构建RESTful服务中，如何同过Hibernate将数据持久化进SQLServer的过程

#二、环境

* 1.上文的项目RestDemo
* 2.SQLServer2005
* 3.jtds数据库连接驱动：下载地址[最新版本](http://sourceforge.net/projects/jtds/files/latest/download?source=files),替换掉上文项目中的`mysql-connector`

#三、配置

1.与上文mysql的配置不同点主要在hibernate.cfg.xml文件；
配置如下：

```xml
<?xml version='1.0' encoding='utf-8'?>  
<!DOCTYPE hibernate-configuration PUBLIC  
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"  
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">  
  
<hibernate-configuration>  
   <session-factory>  
   <!-- Database connection settings -->  
        <property name="connection.driver_class">net.sourceforge.jtds.jdbc.Driver</property>  
        <property name="connection.url">jdbc:jtds:sqlserver://192.168.1.10:1433;RestDemo</property>  
        <property name="connection.username">sa</property>  
        <property name="connection.password">aA123456</property>  
        <property name="hibernate.default_schema">RestDemo</property>
        <!-- JDBC connection pool (use the built-in) -->  
        <property name="connection.pool_size">1</property>  
        <!-- SQL dialect -->  
        <property name="dialect">org.hibernate.dialect.SQLServerDialect</property>  
        <!-- Enable Hibernate's automatic session context management -->  
        <property name="current_session_context_class">thread</property>  
        <!-- Disable the second-level cache  -->  
        <property name="cache.provider_class">org.hibernate.cache.internal.NoCacheProvider</property>  
        <!-- Echo all executed SQL to stdout -->  
        <property name="show_sql">true</property>  
        <!-- Drop and re-create the database schema on startup -->  
        <property name="hbm2ddl.auto">update</property>  
        <mapping resource="com/waylau/rest/bean/User.hbm.xml"/>  
      </session-factory>  
</hibernate-configuration>  
```
2.修改于mysql不兼容的sql语句`com.waylau.rest.dao.impl`中的`UserDaoImpl`:

getUserById修改成如下：

	@Override
	public User getUserById(String id) {
        SessionFactory sessionFactory = HibernateUtil.getSessionFactory(); 
		Session s = null;
		Transaction t = null;
		User user = null;
		try{
		 s = sessionFactory.openSession();
		 t = s.beginTransaction();
		 String hql = "from User where userId='"+id+"'";  
		 Query query = s.createQuery(hql);  
		 user = (User) query.uniqueResult(); 
		 t.commit();
		}catch(Exception err){
		t.rollback();
		err.printStackTrace();
		}finally{
		s.close();
		}
		return user;
	}


getAllUsers给成如下：

	@Override
	public List<User> getAllUsers() {
        SessionFactory sessionFactory = HibernateUtil.getSessionFactory(); 
		Session s = null;
		Transaction t = null;
		List<User> uesrs = null;
		try{
		 s = sessionFactory.openSession();
		 t = s.beginTransaction();
		 String hql = "select * from [RestDemo].dbo.t_user";  
		 Query query = s.createSQLQuery(hql).addEntity(User.class);  
         //query.setCacheable(true); // 设置缓存  
         uesrs = query.list();  
		 t.commit();
		}catch(Exception err){
		t.rollback();
		err.printStackTrace();
		}finally{
		s.close();
		}
		return uesrs;
	}


或者如下：


	@Override
	public List<User> getAllUsers() {
        SessionFactory sessionFactory = HibernateUtil.getSessionFactory(); 
		Session s = null;
		Transaction t = null;
		List<User> uesrs = null;
		try{
		 s = sessionFactory.openSession();
		 t = s.beginTransaction();
		 String hql = " from User";  
		 Query query = s.createQuery(hql);  
         //query.setCacheable(true); // 设置缓存  
         uesrs = query.list();  
		 t.commit();
		}catch(Exception err){
		t.rollback();
		err.printStackTrace();
		}finally{
		s.close();
		}
		return uesrs;
	}


#四、问题

##可能会出现如下错误

	ERROR: 指定的架构名称 "RestDemo" 不存在，或者您没有使用该名称的权限。
	三月 26, 2014 3:38:43 下午 org.hibernate.tool.hbm2ddl.SchemaUpdate execute
	INFO: HHH000232: Schema update complete
	Hibernate: insert into RestDemo.T_USER (userName, age, USERID) values (?, ?, ?)
	三月 26, 2014 3:38:43 下午 org.hibernate.engine.jdbc.spi.SqlExceptionHelper logExceptions
	WARN: SQL Error: 208, SQLState: S0002
	三月 26, 2014 3:38:43 下午 org.hibernate.engine.jdbc.spi.SqlExceptionHelper logExceptions
	ERROR: 对象名 'RestDemo.T_USER' 无效。

##解决方案：

将配置文件中的`hibernate.default_schema`值修改为如下即可：

	<property name="hibernate.default_schema">RestDemo.dbo</property>  

或者去掉上面的配置，在“User.hbm.xml”修改如下

    <class name="User" table="T_USER" schema="RestDemo.dbo">  



**本章源码**：[https://github.com/waylau/RestDemo/tree/master/jersey-demo6-sqlserver-hibernate](https://github.com/waylau/RestDemo/tree/master/jersey-demo6-sqlserver-hibernate)

[https://github.com/waylau/RestDemo/tree/master/jersey-demo6.2-sqlserver-hibernate](https://github.com/waylau/RestDemo/tree/master/jersey-demo6.2-sqlserver-hibernate)