---
layout: post
title:  Spirng 4 、Hibernate 4 事务管理
date: 2015-01-03 01:21
author: admin
comments: true
categories: [Spring,Hibernate]
tags: [Spring,Hibernate]
---

建议将 Hibernate SessionFactory 交给　Spring 进行事务管理,在 applicationContext.xml 里面配置

<!-- 配置sessionFactory -->
	<bean id="sessionFactory"
		class="org.springframework.orm.hibernate4.LocalSessionFactoryBean">
		<!-- 数据源 -->
		<property name="dataSource" ref="dataSource" />

		<!-- hibernate的相关属性配置 -->
		<property name="hibernateProperties">
			<props>
			...
	</bean>

	<!-- 定义事务管理 -->
	<bean id="transactionManager"
		class="org.springframework.orm.hibernate4.HibernateTransactionManager">
		<property name="sessionFactory" ref="sessionFactory" />
		<!-- 自动扫描实体对象 com.waylau.entity 的包结构中存放实体类 -->
		<property name="packagesToScan" value="com.waylau.entity" />
	</bean>

使用 SessionFactory

    @Autowired
    private SessionFactory sessionFactory;
 
    public Session getSession() {
        return sessionFactory.getCurrentSession();
    }
 
<!-- more -->

##延伸

声明事务

###方法1

在调用的类或者方法上加 `@Transactional`注解

	/**
	 * @author waylau.com 2015年1月3日
	 */
	@RunWith(SpringJUnit4ClassRunner.class)
	@ContextConfiguration(locations = { "classpath:applicationContext.xml" })
	@Transactional
	public class TestSdUserDaoImpl {
	
		@Autowired
		private SdUserDaoImpl sdUserDaoImpl;
			
		@Test
		public void countAllTest() {
			System.out.println(sdUserDaoImpl.countAll());
		}
		@Test
		public void findByIdTest() {
			SdUser e = sdUserDaoImpl.findById(1);
			
			System.out.println(e.getRealname());
		}
		
	}

###方法2

用 Spring AOP


	<!-- 扫描有注解的文件 base-package 包路径 -->
	<context:component-scan base-package="com" />

	<tx:advice id="txAdvice" transaction-manager="transactionManager">
		<tx:attributes>
			<!-- 事务执行方式 REQUIRED：指定当前方法必需在事务环境中运行， 如果当前有事务环境就加入当前正在执行的事务环境， 如果当前没有事务，就新建一个事务。 
				这是默认值。 -->
			<tx:method name="create*" propagation="REQUIRED" />
			<tx:method name="save*" propagation="REQUIRED" />
			<tx:method name="add*" propagation="REQUIRED" />
			<tx:method name="update*" propagation="REQUIRED" />
			<tx:method name="remove*" propagation="REQUIRED" />
			<tx:method name="del*" propagation="REQUIRED" />
			<tx:method name="import*" propagation="REQUIRED" />
			<!-- 指定当前方法以非事务方式执行操作，如果当前存在事务，就把当前事务挂起，等我以非事务的状态运行完，再继续原来的事务。 查询定义即可 
				read-only="true" 表示只读 -->
			<tx:method name="get*" propagation="REQUIRED" read-only="true" />
			<tx:method name="find*" propagation="REQUIRED" read-only="true" />
			<tx:method name="load*" propagation="REQUIRED" read-only="true" />
			<tx:method name="*" propagation="NOT_SUPPORTED" read-only="true" />

		</tx:attributes>
	</tx:advice>

	<!-- 定义切面，在 com.emsc.service.impl.*.*（..） 中执行有关的hibernate session的事务操作 -->
	<aop:config>
		<aop:pointcut id="serviceOperation"
			expression="execution(* com.emsc.service..*.*(..))" />
		<aop:advisor advice-ref="txAdvice" pointcut-ref="serviceOperation" />
	</aop:config>

这样就定义了那些类或者那些方法需要执行事务

##问题

假如如此配置还有事务上的问题，如下：

	org.hibernate.HibernateException: Could not obtain transaction-synchronized Session for current thread
	at org.springframework.orm.hibernate4.SpringSessionContext.currentSession(SpringSessionContext.java:134)
	at org.hibernate.internal.SessionFactoryImpl.getCurrentSession(SessionFactoryImpl.java:1014)
	 ...

如果用 aop 有出现问题，请将`<aop:config>` 改为 `<aop:config proxy-target-class="true">`