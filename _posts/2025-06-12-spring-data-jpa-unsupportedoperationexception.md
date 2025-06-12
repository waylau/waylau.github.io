---
layout: post
title: Spring Data JPA修改不可变集合导致UnsupportedOperationException错误分析   
date: 2025-06-12 00:22
author: admin
comments: true
categories: [Spring Data]
tags: [Java,Spring Data,JPA]
---

本文分析 UnsupportedOperationException 错误原因，并提出了整改方案。

<!-- more -->


## 问题

执行 noteRepository.save(note) 时候报 java.lang.UnsupportedOperationException：

```
java.lang.UnsupportedOperationException: null
	at java.base/java.util.AbstractList.remove(AbstractList.java:169) ~[na:na]
	at java.base/java.util.AbstractList$Itr.remove(AbstractList.java:389) ~[na:na]
	at java.base/java.util.AbstractList.removeRange(AbstractList.java:600) ~[na:na]
	at java.base/java.util.AbstractList.clear(AbstractList.java:245) ~[na:na]
	at org.hibernate.type.CollectionType.replaceElements(CollectionType.java:506) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.type.CollectionType.replace(CollectionType.java:719) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.type.TypeHelper.replace(TypeHelper.java:117) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.event.internal.DefaultMergeEventListener.copyValues(DefaultMergeEventListener.java:596) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.event.internal.DefaultMergeEventListener.entityIsPersistent(DefaultMergeEventListener.java:286) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.event.internal.DefaultMergeEventListener.merge(DefaultMergeEventListener.java:220) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.event.internal.DefaultMergeEventListener.doMerge(DefaultMergeEventListener.java:152) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.event.internal.DefaultMergeEventListener.onMerge(DefaultMergeEventListener.java:136) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.event.internal.DefaultMergeEventListener.onMerge(DefaultMergeEventListener.java:89) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.event.service.internal.EventListenerGroupImpl.fireEventOnEachListener(EventListenerGroupImpl.java:127) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.internal.SessionImpl.fireMerge(SessionImpl.java:854) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at org.hibernate.internal.SessionImpl.merge(SessionImpl.java:840) ~[hibernate-core-6.6.15.Final.jar:6.6.15.Final]
	at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
	at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
	at org.springframework.orm.jpa.ExtendedEntityManagerCreator$ExtendedEntityManagerInvocationHandler.invoke(ExtendedEntityManagerCreator.java:364) ~[spring-orm-6.2.7.jar:6.2.7]
	at jdk.proxy2/jdk.proxy2.$Proxy120.merge(Unknown Source) ~[na:na]
	at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
	at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
	at org.springframework.orm.jpa.SharedEntityManagerCreator$SharedEntityManagerInvocationHandler.invoke(SharedEntityManagerCreator.java:320) ~[spring-orm-6.2.7.jar:6.2.7]
	at jdk.proxy2/jdk.proxy2.$Proxy120.merge(Unknown Source) ~[na:na]
	at org.springframework.data.jpa.repository.support.SimpleJpaRepository.save(SimpleJpaRepository.java:654) ~[spring-data-jpa-3.5.0.jar:3.5.0]
	at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:104) ~[na:na]
	at java.base/java.lang.reflect.Method.invoke(Method.java:565) ~[na:na]
	at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:359) ~[spring-aop-6.2.7.jar:6.2.7]
	at org.springframework.data.repository.core.support.RepositoryMethodInvoker$RepositoryFragmentMethodInvoker.lambda$new$0(RepositoryMethodInvoker.java:277) ~[spring-data-commons-3.5.0.jar:3.5.0]
	at org.springframework.data.repository.core.support.RepositoryMethodInvoker.doInvoke(RepositoryMethodInvoker.java:170) ~[spring-data-commons-3.5.0.jar:3.5.0]
	at org.springframework.data.repository.core.support.RepositoryMethodInvoker.invoke(RepositoryMethodInvoker.java:158) ~[spring-data-commons-3.5.0.jar:3.5.0]
	at org.springframework.data.repository.core.support.RepositoryComposition$RepositoryFragments.invoke(RepositoryComposition.java:515) ~[spring-data-commons-3.5.0.jar:3.5.0]
	at org.springframework.data.repository.core.support.RepositoryComposition.invoke(RepositoryComposition.java:284) ~[spring-data-commons-3.5.0.jar:3.5.0]
	at org.springframework.data.repository.core.support.RepositoryFactorySupport$ImplementationMethodExecutionInterceptor.invoke(RepositoryFactorySupport.java:734) ~[spring-data-commons-3.5.0.jar:3.5.0]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
	at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.doInvoke(QueryExecutorMethodInterceptor.java:174) ~[spring-data-commons-3.5.0.jar:3.5.0]
	at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.invoke(QueryExecutorMethodInterceptor.java:149) ~[spring-data-commons-3.5.0.jar:3.5.0]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
	at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:380) ~[spring-tx-6.2.7.jar:6.2.7]
	at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:119) ~[spring-tx-6.2.7.jar:6.2.7]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
	at org.springframework.dao.support.PersistenceExceptionTranslationInterceptor.invoke(PersistenceExceptionTranslationInterceptor.java:138) ~[spring-tx-6.2.7.jar:6.2.7]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
	at org.springframework.data.jpa.repository.support.CrudMethodMetadataPostProcessor$CrudMethodMetadataPopulatingMethodInterceptor.invoke(CrudMethodMetadataPostProcessor.java:165) ~[spring-data-jpa-3.5.0.jar:3.5.0]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.2.7.jar:6.2.7]
	at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:223) ~[spring-aop-6.2.7.jar:6.2.7]
	at jdk.proxy2/jdk.proxy2.$Proxy132.save(Unknown Source) ~[na:na]
	at com.waylau.rednote.service.impl.NoteServiceImpl.updateNote(NoteServiceImpl.java:86) ~[classes/:na]
```

## 分析

核心代码位置：

```java
@Override
public void updateNote(Note note, NoteEditDto noteEditDto) {

	// 更新基本信息
	note.setTitle(noteEditDto.getTitle());
	note.setContent(noteEditDto.getContent());
	note.setCategory(noteEditDto.getCategory());

	// 字符串转为List
	note.setTopics(StringUtil.splitToList(noteEditDto.getTopics()," "));

	// 保存更新
	noteRepository.save(note);
}
```


其中，实体Note的topics是由StringUtil.splitToList()生成的。splitToList实现如下：


```java
public static List<String> splitToList(String source, String regex) {
	if (source.isEmpty()) {
		return Collections.emptyList();
	}

	return  Arrays.asList(source.split(regex));
}
```


`Arrays.asList()` 返回的集合是不可变集合，而 Hibernate 在执行持久化操作时需要修改这些集合。



## 调试建议



在保存前临时替换集合：

```java
@Override
public void updateNote(Note note, NoteEditDto noteEditDto) {
	// 更新基本信息
	note.setTitle(noteEditDto.getTitle());
	note.setContent(noteEditDto.getContent());
	note.setCategory(noteEditDto.getCategory());

	// 字符串转为List
	// 确保体使用可变集合实现
	// note.setTopics(StringUtil.splitToList(noteEditDto.getTopics()," "));
	note.setTopics(new ArrayList<>(StringUtil.splitToList(noteEditDto.getTopics()," ")));
	// 保存更新
	noteRepository.save(note);
}
```


### 总结

`UnsupportedOperationException` 通常表示你正在尝试修改一个不可变集合。确保你的实体使用可变集合实现（如 `ArrayList`），并在DTO到实体转换过程中创建新的可变集合实例。