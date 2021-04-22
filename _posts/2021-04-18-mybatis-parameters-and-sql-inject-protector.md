---
layout: post
title: MyBatis的`${}`和`#{}`的区别及SQL防注入的方法
date: 2021-04-18 00:22
author: admin
comments: true
categories: [MyBatis]
tags: [MyBatis,SQL]
---

本文介绍了MyBatis的`${}`和`#{}`的用法区别，以及针对$可能带来的风险提供一种简易的SQL防注入的方法。

<!-- more -->


 

## `#{}`用法

select语句是MyBatis中最常用的元素之一，例如：

 
```xml
<select id="selectPerson" parameterType="int" resultType="hashmap">

SELECT * FROM PERSON WHERE ID = #{id}

</select>
```
 

此语句称为selectPerson，采用int（或Integer）类型的参数，并将查询结果封装为HashMap作为返回。

 

语句中`#{id}`这告诉MyBatis创建一个PreparedStatement参数。对于JDBC而言，这样的参数类似于PreparedStatement语句中的“?”标识，如下：

 
```java
// JDBC代码
String selectPerson = "SELECT * FROM PERSON WHERE ID=?";

PreparedStatement ps = conn.prepareStatement(selectPerson);
ps.setInt(1,id);
```
 

## `${}`用法
 

默认情况下，使用`#{}`语法将导致MyBatis生成PreparedStatement属性，并根据PreparedStatement参数安全地设置值（例如“?”标识）。虽然这更安全、更快，而且几乎总是首选，但有时只是想直接将未修改的字符串注入SQL语句。例如，对于订单排序，可以使用类似的内容：

 
```xml
ORDER BY ${columnName}
```
 

在上面的用法中，MyBatis不会修改或转义字符串。

 

但需要注意的是： `${}`用法如果是直接接受用户的输入，将未经修改的语句注入到程序是不安全的。这将导致潜在的SQL注入攻击风险。

 

 

## 针对`${}`的SQL防注入器

`${}`用法存在SQL注入的风险，因此需要对用户的输入内容进行校验。这里提供一个简易的SQL防注入的方法。

 
```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

/**
 * SQL注入防护器
 *
 * @author waylau.com
 * @since 2021-04-18
 */
public class SqlInjectionProtector {
    private static final String SPECIAL_CHAR = "\\W";

    /**
     * 校验SQL语句是否合法
     * 如果非法，则抛出异常
     *
     * @param statement 语句
     * @return 是否合法
     * @throws IllegalArgumentException 校验非法则抛出此异常
     */
    public static boolean verifySqLStatement(String statement) {
        if (!StringUtility.isEmpty(statement)) {
            if (isSpecialChar(statement)) {
                throw new IllegalArgumentException("illegal statement: " + statement);
            }
        }

        return Boolean.TRUE;
    }

    /**
     * 判断是否含有特殊字符
     *
     * @param str 校验的字符串
     * @return 是否特殊字符
     */
    public static boolean isSpecialChar(String str) {
        Pattern pattern = Pattern.compile(SPECIAL_CHAR);
        Matcher matcher = pattern.matcher(str);
        return matcher.find();
    }

}
```
 

 

上述代码核心思想是，通过正则表达式的方式，来检测出特殊字符。有特殊字符，就抛出异常，中断程序继续往下运行。

 

何为特殊字符？针对ORDER BY ${columnName} 这个例子而言，字段名的所使用的字符是有一定限制的，限制只能使用[a-z0-9A-Z_]这个范围内的字符。因此超出这个范围内的所有字符，即为特殊字符。在正则表达式里面，非[a-z0-9A-Z_]范围内的字符，可以用“\\W”表示。

 

以下是测试用例

 
```java
import static org.junit.Assert.assertFalse;
import static org.junit.Assert.assertTrue;

import org.junit.Test;

/**
* SqlInjectionProtector Test
*
* @author waylau.com
* @since 2021-04-18
*/
public class SqlInjectionProtectorTest {

    @Test
    public void testIsSpecialChar() {
        assertFalse(SqlInjectionProtector.isSpecialChar("user_name"));
        assertTrue(SqlInjectionProtector.isSpecialChar("user_name!"));
        assertTrue(SqlInjectionProtector.isSpecialChar("user_name@"));
        assertTrue(SqlInjectionProtector.isSpecialChar("user_name^"));
        assertTrue(SqlInjectionProtector.isSpecialChar("user_name ")); // 空格
        assertTrue(SqlInjectionProtector.isSpecialChar("insert\ninto\nuser_t")); // 换行符
        assertTrue(SqlInjectionProtector.isSpecialChar("user_name|user_t"));
    }

    @Test
    public void testVerifySqLStatement() {
        assertTrue(SqlInjectionProtector.verifySqLStatement("user_name"));
    }
}
```

 


## 参考引用

* 本文同步至: <https://waylau.com/mybatis-parameters-and-sql-inject-protector/>
* 《轻量级Java EE企业应用开发实战》（<https://item.jd.com/12817685.html>）
* 《大型互联网应用轻量级架构实战》（<https://item.jd.com/12629095.html>）
