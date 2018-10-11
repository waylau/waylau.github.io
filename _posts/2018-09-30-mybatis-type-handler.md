---
layout: post
title: MyBatis使用自定义TypeHandler转换类型    
date: 2018-09-30 00:22
author: admin
comments: true
categories: [MyBatis]
tags: [MyBatis,TypeHandler]
---

MyBatis虽然有很好的SQL执行性能，但毕竟不是完整的ORM框架，不同的数据库之间SQL执行还是有差异。
笔者最近在升级 Oracle 驱动至 ojdbc 7 ，就发现了处理DATE类型存在问题。还好MyBatis提供了使用自定义TypeHandler转换类型的功能。

本文介绍如下使用 TypeHandler 实现日期类型的转换。

<!-- more -->


## 问题背景
 
项目中有如下的字段，是采用的DATE类型：

```
birthday = #{birthday, jdbcType=DATE},
```

在更新 Oracle 驱动之前，DateOnlyTypeHandler会做出处理，将 jdbcType 是 DATE 的数据转为短日期格式（‘年月日’）插入数据库。毕竟是生日嘛，只需要精确到年月日即可。

但是，升级 Oracle 驱动至 ojdbc 7 ，就发现了处理DATE类型存在问题。插入的数据格式变成了长日期格式（‘年月日时分秒’），显然不符合需求了。


## 解决方案：

MyBatis提供了使用自定义TypeHandler转换类型的功能。可以自己写个TypeHandler来对 DATE 类型做特殊处理：


```java
/**
 * Welcome to https://waylau.com
 */
package com.waylau.lite.mall.type;

import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.text.DateFormat;
import java.util.Date;

import org.apache.ibatis.type.BaseTypeHandler;
import org.apache.ibatis.type.JdbcType;
import org.apache.ibatis.type.MappedJdbcTypes;
import org.apache.ibatis.type.MappedTypes;

/**
 * 自定义TypeHandler，用于将日期转为'yyyy-MM-dd'
 * 
 * @since 1.0.0 2018年10月10日
 * @author <a href="https://waylau.com">Way Lau</a>
 */
@MappedJdbcTypes(JdbcType.DATE)
@MappedTypes(Date.class)
public class DateShortTypeHandler extends BaseTypeHandler<Date> {

	@Override
	public void setNonNullParameter(PreparedStatement ps, int i, Date parameter, JdbcType jdbcType)
			throws SQLException {
		DateFormat df = DateFormat.getDateInstance();
		String dateStr = df.format(parameter);
		ps.setDate(i, java.sql.Date.valueOf(dateStr));
	}

	@Override
	public Date getNullableResult(ResultSet rs, String columnName) throws SQLException {
		java.sql.Date sqlDate = rs.getDate(columnName);
		if (sqlDate != null) {
			return new Date(sqlDate.getTime());
		}
		return null;
	}

	@Override
	public Date getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
		java.sql.Date sqlDate = rs.getDate(columnIndex);
		if (sqlDate != null) {
			return new Date(sqlDate.getTime());
		}
		return null;
	}

	@Override
	public Date getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
		java.sql.Date sqlDate = cs.getDate(columnIndex);
		if (sqlDate != null) {
			return new Date(sqlDate.getTime());
		}
		return null;
	}

}
```

如果是 Spring 项目，以下面方式进行 TypeHandler 的配置：

```xml
<!-- 自定义 -->
<!--声明TypeHandler bean-->
<bean id="dateShortTypeHandler" class="com.waylau.lite.mall.type.DateShortTypeHandler"/>

<!-- MyBatis 工厂 -->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="dataSource" />
	
	<!--TypeHandler注入-->
	<property name="typeHandlers" ref="dateShortTypeHandler"/>
</bean>
```

## 如何使用 TypeHandler

### 方式1 ：指定 jdbcType 为 DATE

比如，目前，项目中有如下的字段，是采用的DATE类型：

```
birthday = #{birthday, jdbcType=DATE},
```

### 方式2 ：指定 typeHandler

指定 typeHandler 为我们自定义的 TypeHandler：

```
birthday = #{birthday, typeHandler=com.waylau.lite.mall.type.DateShortTypeHandler},
```



## 源码：

见<https://github.com/waylau/lite-book-mall>