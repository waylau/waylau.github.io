---
layout: post
title: Hibernate tools介绍、安装、使用
date: 2014-08-02 01:40
author: admin
comments: true
categories: [Hibernate]
tags: [Hibernate]
---

##介绍
[http://hibernate.org/tools/](http://hibernate.org/tools/)

##版本
[http://tools.jboss.org/downloads/overview.html](http://tools.jboss.org/downloads/overview.html)

选择对应的版本

至今（2014-12-14） `Eclipse Luna 4.4`的最新版本是 `JBoss Tools  4.2.1.Final

##下载

下载安装方式有如下三种：

###1.从marketplace

[http://tools.jboss.org/downloads/jbosstools/luna/4.2.0.Beta3.html#marketplace](http://tools.jboss.org/downloads/jbosstools/luna/4.2.0.Beta3.html#marketplace)

###2.URL站点更新

[http://tools.jboss.org/downloads/jbosstools/luna/4.2.0.Beta3.html#update_site](http://tools.jboss.org/downloads/jbosstools/luna/4.2.0.Beta3.html#update_site)

###3.插件下载

[http://tools.jboss.org/downloads/jbosstools/luna/4.2.0.Beta3.html#zips](http://tools.jboss.org/downloads/jbosstools/luna/4.2.0.Beta3.html#zips)


##使用

<img src="http://99btgc01.info/uploads/2015/01/htools7.jpg" alt="htools7.jpg" title="htools7.jpg" />

###1. 创建Hibernate Configuration File(cfg.xml)

根据要求填入相关配置，测试下能否连接上数据库就行了

###2. 创建Hibernate Console Configuration

选择项目和数据库连接（连接 emsc 在上一步中已经创建了）

<img src="http://99btgc01.info/uploads/2015/01/htools8.jpg" alt="htools8.jpg" title="htools8.jpg" />

选择方言

<img src="http://99btgc01.info/uploads/2015/01/htools9.jpg" alt="htools9.jpg" title="htools9.jpg" />

###3. 使用 Hibernate Code Generation Configurations 自动生成实体

<img src="http://99btgc01.info/uploads/2015/01/htools3.jpg" alt="htools3.jpg" title="htools3.jpg" />

选择 上一步中创建好的 Console Configuration，填写包名

<img src="http://99btgc01.info/uploads/2015/01/htool.jpg" alt="htool.jpg" title="htool.jpg" />

选择要生成的的文件类型

<img src="http://99btgc01.info/uploads/2015/01/htools2.jpg" alt="htools2.jpg" title="htools2.jpg" />


会有一个 “The serializable class SdUser does not declare a static final serialVersionUID field of type long” 的警告

<img src="http://99btgc01.info/uploads/2015/01/htools4.jpg" alt="htools4.jpg" title="htools4.jpg" />

用 eclipse 自动处理这个警告，生成一个 serial version ID 即可

<img src="http://99btgc01.info/uploads/2015/01/htools5.jpg" alt="htools5.jpg" title="htools5.jpg" />

完成

<img src="http://99btgc01.info/uploads/2015/01/htools6.jpg" alt="htools6.jpg" title="htools6.jpg" />

##错误解决
 
	Problems while reading database schema
	
	org.hibernate.HibernateException: could not instantiate RegionFactory 

换个项目没有问题。jar 冲突。换不了jar 就再另外一个项目里面把bean ,xml 给生成完了再把代码移回原项目

##参考

* [https://docs.jboss.org/tools/4.1.0.Final/en/hibernatetools/html_single/index.html](https://docs.jboss.org/tools/4.1.0.Final/en/hibernatetools/html_single/index.html)
