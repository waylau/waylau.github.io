---
layout: post
title: Thymeleaf 3 引入了新的解析系统
date: 2017-02-19 00:22
author: admin
comments: true
categories: [GFM]
tags: [GFM,Eclipse]
---


如果你的代码使用了 HTML5 的标准，而Thymeleaf 版本来停留在 2.x ，那么如果没有把`<input>`闭合，如下：

```
<form>
 First name:<br>
<input type="text" name="firstname">
<br>
 Last name:<br>
<input type="text" name="lastname">
</form> 
```

就会抛出如下错误。

```
org.xml.sax.SAXParseException: 元素类型 "input" 必须由匹配的结束标记 "</input>" 终止。
```


<!-- more -->

## 解决方案

### 1. 沿用 Thymeleaf 老版本

如果你的 Thymeleaf 不能变更，那么你的 HTML 标准也只能停留在老版本了。你必须严格遵守 XML 定义，在`<input>`加上结束标记`</input>`。这显然，对于 HTML5 不友好。
 
### 2. 升级至 Thymeleaf 3 新版本

是时候尝试下使用 Thymeleaf 3 了。Thymeleaf 3 使用了新的解析系统。

Thymeleaf 3 不再是基于XML结构的。由于引入新的解析引擎，模板的内容格式不再需要严格遵守XML规范。即不在要求标签闭合，属性加引号等等。当然，出于易读性考虑，还是推荐你按找XML的标准去编写模板。

Thymeleaf 3 使用一个名为 [AttoParser 2](http://www.attoparser.org/)的新解析器。 一个新的、基于事件（不符合SAX标准）的解析器，AttoParser由 Thymeleaf 的作者开发，符合 Thymeleaf 的风格。

AttoParser 提供 Thymeleaf 3 两个重要功能：

* 完全支持XML和HTML5（非XML化）标记，从而不再需要外部标记平衡操作。
* 无损解析，以便在处理的输出的标记类似于具有最高精度的原始模板。


所以下面的格式在 Thymeleaf 3 里面是合法的：

```
<div><img alt=logo th:src='@{/images/logo.png}'>
```

## Thymeleaf 3 其他方面的解析改进

### 1. 启用验证的解析

在 Thymeleaf 2.1提供了两种`VALID*`模板模式，名为`VALIDXHTML`和`VALIDXML`，在而 Thymeleaf 3 中将不再存在。 新的解析基础结构不提供HTML或XML验证，即在解析期间无法验证模板标记是否符合指定的DTD或XML模式定义。

### 2. 在<script> 或 style 不再需要 <![CDATA[ ... ]]>  


Thymeleaf 2.1 要求将`<script>`标记的内容封装在 CDATA 中，以便所使用的任何`<`或`>`符号不会干扰基于XML的解析：


```
<script>
/*<![CDATA[*/
  var user = ...
  if (user.signupYear < 1990) {
    alert('You\'ve been here for a long time!');
  }
/*]]>*/
</script>
```


而在 Thymeleaf 3 中则不需要这样做，代码立马变得简洁干净：

```
<script>
  var user = ...
  if (user.signupYear < 1990) {
    alert('You\'ve been here for a long time!');
  }
</script>
```

## 参考文献

* https://github.com/thymeleaf/thymeleaf/issues/390
 
