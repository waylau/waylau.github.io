---
layout: post
title: Activiti 5.12.1 下载、安装和运行
date: 2013-05-18 01:42
author: admin
comments: true
categories: [Activiti]
---
一、下载和安装

activiti下载：

本例子版本：activiti-5.12.1.zip

<a href="http://www.activiti.org/download.html">http://www.activiti.org/download.html</a>

&nbsp;

JDK 下载 JDK 6+

本例子版本：jdk7

<a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html">http://www.oracle.com/technetwork/java/javase/downloads/index.html</a>

eclipse下载 Eclipse Indigo and Juno：

本例子版本：Eclipse Juno (4.2)

<a href="http://www.eclipse.org/downloads/download.php?file=/technology/epp/downloads/release/juno/SR2/eclipse-jee-juno-SR2-win32.zip">http://www.eclipse.org/downloads/download.php?file=/technology/epp/downloads/release/juno/SR2/eclipse-jee-juno-SR2-win32.zip</a>

&nbsp;

解压activiti-5.12.1.zip获取activiti-explorer.war

<a href="http://www.waylau.com/activiti-5-12-1-download-install-and-run/001-11/" rel="attachment wp-att-374"><img alt="001" src="http://www.waylau.com/wp-content/uploads/2013/05/0012-300x73.jpg" width="300" height="73" /></a>

&nbsp;

activiti-explorer.war 拷贝至Tomcat.（或其他java web服务器,本例为Tomcat 6.0）的webapps目录下

二、运行

启动tomcat，在浏览器打开项目（本例子tomcat端口为8089）

<a href="http://localhost:8089/activiti-explorer/">http://localhost:8089/activiti-explorer/</a>

&nbsp;

<a href="http://www.waylau.com/activiti-5-12-1-download-install-and-run/003-4/" rel="attachment wp-att-375"><img alt="003" src="http://www.waylau.com/wp-content/uploads/2013/05/003-300x227.jpg" width="300" height="227" /></a>

&nbsp;

见到这个界面说明项目运行成功了!

默认使用的是内存数据库 H2，下面为数据库表
<p align="left"><b>Table </b><b>用户和分组</b></p>

<table border="0" cellspacing="0" cellpadding="0">
<thead>
<tr>
<td width="63">
<p align="center">UserId</p>
</td>
<td width="84">
<p align="center">Password</p>
</td>
<td width="108">
<p align="center">Security roles</p>
</td>
</tr>
</thead>
<tbody>
<tr>
<td width="63">
<p align="left">kermit</p>
</td>
<td width="84">
<p align="left">kermit</p>
</td>
<td width="108">
<p align="left">admin</p>
</td>
</tr>
<tr>
<td width="63">
<p align="left">gonzo</p>
</td>
<td width="84">
<p align="left">gonzo</p>
</td>
<td width="108">
<p align="left">manager</p>
</td>
</tr>
<tr>
<td width="63">
<p align="left">fozzie</p>
</td>
<td width="84">
<p align="left">fozzie</p>
</td>
<td width="108">
<p align="left">user</p>
</td>
</tr>
</tbody>
</table>
&nbsp;

可以使用上面的账号登入系统（本例为kermit）

<a href="http://www.waylau.com/activiti-5-12-1-download-install-and-run/004-2/" rel="attachment wp-att-376"><img alt="004" src="http://www.waylau.com/wp-content/uploads/2013/05/004-300x187.jpg" width="300" height="187" /></a>

&nbsp;
