---
layout: post
title: CentOS 7 安装 Tomcat
date: 2015-05-15 01:41
author: admin
comments: true
categories: [CentOS,Tomcat]
tags: [CentOS,Tomcat]
---

## 环境

* CentOS 7.1 (64-bit system)
* JDK 1.8.0_45（jdk-8u45）
* Tomcat 8.0.22 (jdk-8u45-linux-x64.tar)

<!-- more -->

## CentOS 安装

参考：<http://www.waylau.com/centos-7-installation-and-configuration/>

### JDK 安装

参考：<http://www.waylau.com/centos-7-install-jdk/>


## 下载

地址：<http://mirrors.cnnic.cn/apache/tomcat/tomcat-8/v8.0.22/bin/apache-tomcat-8.0.22.tar.gz>

本例中，下载包存放在 `/home/software` 目录下

执行下载命令：

    wget http://mirrors.cnnic.cn/apache/tomcat/tomcat-8/v8.0.22/bin/apache-tomcat-8.0.22.tar.gz

## 解压

    tar xvfz apache-tomcat-8.0.22.tar.gz
    
完成后在  `/home/software`  目录下可以看到解压后的 apache-tomcat-8.0.22 包

## 安装

将上述 apache-tomcat-8.0.22 包移到 /usr/local  目录下

    mv apache-tomcat-8.0.22 /usr/local
    
## 配置环境变量

### 方法1

修改 profile 文件

    vi /etc/profile

在文件的末尾修改

  	export JAVA_HOME=/usr/java/jdk1.8.0_45
  	export PATH=$JAVA_HOME/bin:$PATH
    export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

为

   	export JAVA_HOME=/usr/java/jdk1.8.0_45
  	export CATALINA_HOME=/usr/local/apache-tomcat-8.0.22   
  	export PATH=$JAVA_HOME/bin:$PATH:$CATALINA_HOME/lib
    export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$CATALINA_HOME/bin
    
注意

JAVA_HOME 是你的 JDK 安装目录

Linux 下用冒号“:”来分隔路径

$PATH / $CLASSPATH / $JAVA_HOME 是用来引用原来的环境变量的值

export 是把这三个变量导出为全局变量。

### 方法2

上述修改 放到 .bash_profile 文件末尾中

这种方法更为安全，它可以把使用这些环境变量的权限控制到用户级别，如果你需要给某个用户权限使用这些环境变量，你只需要修改其个人用户主目录下的 .bash_profile文件就可以了。

## 启动、关闭

启动执行 

    /usr/local/apache-tomcat-8.0.22/bin/startup.sh
    
或者以控制台打印形式执行 

    /usr/local/apache-tomcat-8.0.22/bin/catalina.sh run


启动成功，提示如下：

    [root@bogon software]# /usr/local/apache-tomcat-8.0.22/bin/startup.sh
    Using CATALINA_BASE:   /usr/local/apache-tomcat-8.0.22
    Using CATALINA_HOME:   /usr/local/apache-tomcat-8.0.22
    Using CATALINA_TMPDIR: /usr/local/apache-tomcat-8.0.22/temp
    Using JRE_HOME:        /usr/java/jdk1.8.0_45
    Using CLASSPATH:       /usr/local/apache-tomcat-8.0.22/bin/bootstrap.jar:/usr/local/apache-tomcat-8.0.22/bin/tomcat-juli.jar
    Tomcat started.

关闭执行 

    /usr/local/apache-tomcat-8.0.22/bin/shutdown.sh
    
或者

    /usr/local/apache-tomcat-8.0.22/bin/catalina.sh stop
    
## 验证

我们在浏览器访问下安装了 Tomcat 的主机 <http://192.168.11.12:8080/> ，出现Tomcat 默认管理界面，说明已经安装启动成功。

![](http://99btgc01.info/uploads/2015/05/tomcat.jpg)

## 问题

如果打不开 Tomcat 默认管理界面，请确认防火墙是否开放了 Tomcat 访问端口

### 方法1：（CentOS 7.x版本之前用法，不推荐）

打开 iptables 的配置文件：

    vi /etc/sysconfig/iptables
    
修改 
    
    *filter
    :INPUT ACCEPT [0:0]
    :FORWARD ACCEPT [0:0]
    :OUTPUT ACCEPT [0:0]
    -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
    -A INPUT -p icmp -j ACCEPT
    -A INPUT -i lo -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
    -A INPUT -j REJECT --reject-with icmp-host-prohibited
    -A FORWARD -j REJECT --reject-with icmp-host-prohibited
    COMMIT

在里面加入这一行：

    -A RH-Firewall-1-INPUT -m state –state NEW -m tcp -p tcp –dport 8080 -j ACCEPT
    
改为

    *filter
    :INPUT ACCEPT [0:0]
    :FORWARD ACCEPT [0:0]
    :OUTPUT ACCEPT [0:0]
    -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
    -A INPUT -p icmp -j ACCEPT
    -A INPUT -i lo -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
    -A RH-Firewall-1-INPUT -m state –state NEW -m tcp -p tcp –dport 8080 -j ACCEPT
    -A INPUT -j REJECT --reject-with icmp-host-prohibited
    -A FORWARD -j REJECT --reject-with icmp-host-prohibited
    COMMIT

>如果该 iptables 配置文件 不存在，先执行 `yum install iptables-services` 安装

执行 iptables 重启生效

    service iptables restart
    
###  方式2：firewall-cmd（推荐）

执行

    firewall-cmd --permanent --zone=public --add-port=8080/tcp
    
这样就开放了相应的端口。

执行 

    firewall-cmd --reload 

使最新的防火墙设置规则生效。


## 设置不项目名称部署

修改 Tomcat 安装目录 conf 文件夹下 server.xml， 在`<host>` 节点下，增加

    <Context path="/" docBase="/home/project/emsc" debug="0" privileged="true"></Context>

其中 path 就是 要显示的项目名，这里是不显示，docBase 就是项目所在的路径。

