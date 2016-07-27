---
layout: post
title: iReport 5.1.0下载、安装、创建数据源、制作报表
date: 2013-05-22 01:44
author: admin
comments: true
categories: [iReport]
---
一、ireport下载最新版

本例为5.1.0 <a href="http://sourceforge.net/projects/ireport/files/?source=navbar">http://sourceforge.net/projects/ireport/files/?source=navbar</a>

二、安装，直接下一步

三、ireport连接数据库，创建数据源

默认数据源是空的 ，需要自己创建数据源，当然也可以选择他的测试数据库

&nbsp;

1.点击图中按钮，弹出窗，点击 new

&nbsp;

2.这里选择的是 Database JDBC Connection

&nbsp;

3.iReport默认的数据库驱动为黑色字体部分，红色字体需另外导入包

&nbsp;

4.工具à选项

&nbsp;

5.在Classpath添加数据库驱动包

这里我们连接的是sql2000数据库，填加一个jtds-1.2.2.jar

&nbsp;

6.再回到之前的那个数据源设置界面

net.sourceforge.jtds.jdbc.Driver 变成了黑色。选中它

&nbsp;

7.JDBC URL中填入数据库的地址和库名，并填入正确的用户名和密码。

本例选用了一个已经存在的数据库。

点击Test，提示test succesful则说明连接成功。

输入Name, 本例为"EEMS"。

点击Save保存设置。

&nbsp;

四、新增报表模版

1.文件 --&gt;new file，选择blank A4 ，点击 launch report wizard

&nbsp;

2.填入报表名称，选择输出的目录

&nbsp;

3.直接填入查询sql语句，或者选择load query导入已有的sql文件

本例选用design query 采用可视化界面来设计sql语句

&nbsp;

双击选中的数据库表，选择要查询的字段

&nbsp;

点击OK，自动生成了sql语句

&nbsp;

4.选择要参与的字段

&nbsp;

这里向右移入所有字段

&nbsp;

5.选择要分组的字段

这里选择loginName字段

&nbsp;

6.出现下面提示，说明模版创建成功！

&nbsp;

五、设计报表

&nbsp;

报表的结构包括如下部分：Title、Page Header、Column Header、Detail、Column Footer、Page Footer、Summary.下面一一的介绍各个部分。

<strong>Title</strong>：为报表的标题部分，如果报表有多页，则只显示在第一页。

<strong>Page Header</strong> ：为报表每页的一个头部名称，如果报表有多页，则每页都会显示。

<strong>Column Header</strong>:可以理解成表头，如果报表有多页，则每页都会显示。

<strong>Detail</strong>：详细记录，迭代列出所有的查询结果，有多少数据都会显示出来（分页）。

<strong>Column Footer</strong>：相当于表尾，如果报表有多页，则每页都会显示。

<strong>Page Footer</strong>:与Page Header对应，每页都会显示。

<strong>Summary</strong>：报表的一些统计信息。比如共有多少页，当前是第几页等信息。

设计报表的要求是：列出所有用户的信息，（查询语句决定。）

1.从组件面板中，拖出一个 Static Text 组件来，放置在报表的Title部分，输入"用户列表"。

&nbsp;

<strong>2.</strong>在Page Header 也同样拖入一个 Static Text 组件，输入"管理员"

3.展开左侧的 Report Inspector，展开Fields子项，

&nbsp;

将图所示的字段，拖入Detail项中，此时会发现，Column Header项中自动填充了Static Text的表头，如图所示：

&nbsp;

在右侧"属性"，也可以对表头进行编辑，重命名等操作。可以调整各组件的位置、大小等信息。

&nbsp;

4.选择 预览方式，这里选HLML

&nbsp;

5.点击预览，选择Preview

&nbsp;

6.显示数据库数据啦，成功啦。~
