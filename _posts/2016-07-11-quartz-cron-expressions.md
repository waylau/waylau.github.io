---
layout: post
title: Quartz Cron Expressions 详解
date: 2016-07-11 01:41
author: admin
comments: true
categories: [Quartz]
tags: [Quartz]
---

[Quartz](http://www.quartz-scheduler.org/) 是一个完全由 Java 编写的开源企业级作业调度框架。在处理 CronTrigger  进行周期性任务触发时，需要进行时间的配置，其中就需要使用 Cron Expressions。本文将对 Cron Expressions 进行详细讲解。

<!-- more -->

## 什么是 Quartz Cron Expressions

[Cron Expressions](https://en.wikipedia.org/wiki/Cron#CRON_expression)（Cron 表达式）是用来展示时间集合的字符串。在不同的系统或者框架上，Cron Expressions 的标准会有所差异。对于 Quartz Cron Expressions 来说，它被用来配置 CronTrigger 实例，由空格隔开分7个子表达式：

1. Seconds （秒）：范围为0-59的整数，可出现`, - * /`四个字符;
2. Minutes（分）：范围为0-59的整数，可出现`, - * /`四个字符;  
3. Hours （时）：范围为0-23的整数，可出现`, - * /`四个字符;  
4. Day-of-Month（日）：范围为1-31的整数，需要注意所选月的总日数，可出现`, - * /? L W C`八个字符; 
5. Month（月）：范围为0-11的整数，或者使用字符串JAN、FEB、MAR、APR、MAY、JUN、JUL、AUG、SEP、OCT、NOV 和 DEC。可出现`, - * /`四个字符;
6. Day-of-Week （周几）：范围为1-7 (1 为周日) ，或者使用字符串 SUN、MON、TUE、WED、THU、FRI 和 SAT。，可出现`, - * /? L W C`八个字符; 
7. Year (年，可选)，可出现`, - * /`四个字符;

例如，Cron Expressions 字符串 `0 0 12 ? * WED` 表示“每个星期三的12点”。

其中表达式中特殊字符的含义为：

* 通配符`*`可以被用来表示域中“每个”可能的值。因此在"Month"域中的`*`表示每个月，而在 Day-Of-Week 域中的`*`则表示“周中的每一天”。
* `/`字符用来表示值的增量，例如, 如果分钟域中放入`0/15`，它表示“从0开始,每隔15分钟”，如果在分钟域中使用`3/20`，则表示“从第3分钟开始，每隔20分钟”或者另外相同的形式就是`3,23,43`。
* `?`字符可以用在 day-of-month 及 day-of-week 域中，它用来表示“没有指定值”。这对于需要指定一个或者两个域的值而不需要对其他域进行设置来说相当有用。 
* `L`字符可以在 day-of-month 及 day-of-week 中使用，这个字符是"last"的简写，但是在两个域中的意义不同。例如，在 day-of-month 域中的`L`表示这个月的最后一天，即，一月的31日，非闰年的二月的28日。如果它用在 day-of-week 中，则表示`7`或者`SAT`。但是如果在 day-of-week 域中，这个字符跟在别的值后面，则表示"当月的最后的周XXX"。例如："6L" 或者 "FRIL"都表示本月的最后一个周五。当使用`L`选项时，最重要的是不要指定列表或者值范围，否则会导致混乱。
* `W`字符用来指定距离给定日最接近的周几（在 day-of-week 域中指定）。例如：如果你为 day-of-month 域指定为`15W`,则表示“距离月中15号最近的周几”。
* `#`表示表示月中的第几个周几。例如：day-of-week 域中的`6#3` 或者 `FRI#3`表示“月中第三个周五”。


## 例子

下面是几个常见的 Quartz Cron Expressions 例子：

* `0 0/5 * * * ?`: 每隔5分钟触发一次
* `10 0/5 * * * ?`: 在每分钟的10秒后每隔5分钟触发一次的表达式(例如. 10:00:10 am, 10:05:10等.)。
* `0 30 10-13 ? * WED,FRI` : 在每个周三和周五的 10：30，11：30，12：30 触发。
* `0 0/30 8-9 5,20 * ?`: 在每个月的5号、20号的8点和10点之间每隔半个小时触发一次且不包括10点，只是8：30，9：00和9：30的表达式。

有些日程需求可能过于复杂而不能用单个表达式表述，例如：9：00到10：00之间每隔5分钟触发一次，下午1：00到10点每隔20分钟触发一次。这个解决方案就是创建两个触发器，两个触发器都运行相同的任务。


下面是构建 CronTrigger 的例子：

导包：

```java
import static org.quartz.TriggerBuilder.*;
import static org.quartz.CronScheduleBuilder.*;
import static org.quartz.DateBuilder.*:
```

实例化一个 CronTrigger，每天 10:42 定期触发：

```java
trigger = newTrigger()
  .withIdentity("trigger3", "group1")
  .withSchedule(dailyAtHourAndMinute(10, 42))
  .forJob(myJobKey)
  .build();
```

或者使用：

```java
trigger = newTrigger()
    .withIdentity("trigger3", "group1")
    .withSchedule(cronSchedule("0 42 10 * * ?"))
    .forJob(myJobKey)
    .build();
```


## 参考引用

* <https://en.wikipedia.org/wiki/Cron#CRON_expression>
* <http://www.quartz-scheduler.org/documentation/quartz-2.2.x/tutorials/tutorial-lesson-06.html>