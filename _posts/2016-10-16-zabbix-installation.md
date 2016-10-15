---
layout: post
title: Zabbix 在不同平台下的安装
date: 2016-10-16 01:41
author: admin
comments: true
categories: [Zabbix]
tags: [Zabbix]
---
 
Zabbix 是一个基于 Web 界面的提供分布式系统监控以及网络监控功能的企业级的开源解决方案。

Zabbix 能监视各种网络参数，保证服务器系统的安全运营，并提供灵活的通知机制以让系统管理员快速定位、解决存在的各种问题。

本节介绍了 Zabbix 在不同平台上的安装和使用过程。
<!-- more -->
截止目前 Zabbix 的最新版本为 3.2.0。Zabbix 官方网站为 <http://www.zabbix.com>。

### 1. 前置条件


#### 硬件

所需硬件条件如下：

* 内存：Zabbix 需要 128M 物理内存和 256M 的磁盘空间，但是更多的内存能够使数据库跑的更快，Zabbix工作的也就更快；
* CPU：这个取决于你选择的数据库引擎和监控的各项参数；
* 其他硬件：若需要 Zabbix 短信通知的支持，则需要串行通讯端口和一个串行 GSM 调制解调器。USB 转串口的转换器也可以。

下面是几个硬件的配置样例：

名称 | 平台	| CPU/内存 | 数据库 | 所监控的主机数
---- | ---- | ---- | ---- | ----
Small	| CentOS | 虚拟设备 | 	MySQL InnoDB |	100
Medium	| CentOS	| 2 CPU cores/2GB	| MySQL InnoDB	| 500
Large	| RedHat Enterprise Linux	| 4 CPU cores/8GB	| RAID10 MySQL InnoDB 或 PostgreSQL	| >1000
Very large	| RedHat Enterprise Linux	| 8 CPU cores/16GB	| Fast RAID10 MySQL InnoDB 或 PostgreSQL	| >10000


#### 支持的平台

由于安全需求和服务器操作的关键任务特性，UNIX 是唯一的操作系统，能够持续提供必要的性能、容错能力和应变能力。Zabbix 可以在市面上最新的 UNIX 操作系统上运行。

Zabbix server 在以下平台上进行了测试：
 
* Linux
* IBM AIX
* FreeBSD
* NetBSD
* OpenBSD
* HP-UX
* Mac OS X
* Solaris
* Windows：所有 2000 以后的桌面或者服务器版本（只支持 Zabbix agent）


Zabbix 可以在其他类 Unix 操作系统的正常工作。
        
#### 软件

Zabbix 基于最新的 Apache web 服务器、领先的数据库引擎和 PHP 脚本语言。

数据库的需求如下：

软件 | 版本 | 备注
---- | ---- | ----
MySQL | 5.0.3 或之后的版本 | MySQL 可以作为 Zabbix 背后的数据库（可选）；InnoDB 引擎是必须的
Oracle	| 10g 或之后的版本 | Oracle 可以作为 Zabbix 背后的数据库（可选）
PostgreSQL | 8.1 或之后的版本 | PostgreSQL 可以作为 Zabbix 背后的数据库（可选）。建议至少使用 PostgreSQL 8.3 版本，会有更好的 VACUUM 性能
SQLite | 3.3.5 或之后的版本 | SQLite 可以作为 Zabbix 背后的数据库（可选）
IBM DB2 | 9.7 或之后的版本 | IBM DB2 可以作为 Zabbix 背后的数据库（可选）。需要注意的是 IBM DB2 的支持是实验性的

为运行 Zabbix  前端还需要下列软件：

软件 | 版本 
---- | ----
Apache | 1.3.12 或之后的版本 
PHP | 5.4.0 或之后的版本 


浏览器要求：

必须启用 Cookie 和 JavaScript。 Google Chrome、Mozilla Firefox、Microsoft Internet Explorer 和 Opera 的最新版本可以支持 Zabbix。其他浏览器（Apple Safari、Konqueror）也可能正常工作。


服务器的要求：

要求 | 描述 
---- | ----
OpenIPMI | 用于支持 IPMI 
libssh2 | 用于支持 SSH 
fping | 用于支持 ICMP ping item 
libcurl | 用于支持 web 监控、VMware 监控、SMTP 认证。其中若需要 SMTP 认证，libcurl 版本不低于 7.20.0
libiksemel | 用于支持 Jabber  
libxml2 | 用于支持 VMware 监控 
net-snmp | 用于支持 SNMP  
 
Java gateway 的 要求：

Java gateway 编译和运行的 Java 版本不低于 1.6 。


#### 数据库容量

Zabbix 配置数据所需的磁盘空间相对是固定的，数据库容量主要取决于以下几个方面：

* 每秒处理的值的数量：这是 Zabbix 服务器每秒接收到的数据的平均值，例如，有300个 item，更新的时间间隔是60秒，那么每秒处理的数据值就是
`3000/60 = 50`；
* 用于历史数据的 Housekeeper 配置：Zabbix 保留值在一个固定的时间，每个新的数据需要一定的磁盘空间和节点；如果保留历史数据30天，每秒收到50个数据，数据的总量是：`(30*24*3600)*50=129.600.000`大约是 130M；根据我们所使用的数据库存储引擎，数据的类型，保留一个单独的数据可能会用40到几百字节，一般是每个数据值90字节，这样就意味着130M 的数据值将需要`130M * 90 bytes = 10.9GB`的磁盘空间。text/log 类型的 item 值的大小难以准确预测，大约500字节；
* 用于趋势数据的 Housekeeper 配置：Zabbix 保留了每小时的`max/min/avg/count`值的集合用于趋势分析和历史周期的图。每小时这个周期是固定不能更改的。Zabbix 数据库每个数据值90字节，那么考虑3000个 item 每年磁盘空间需要`3000*24*365* 90 = 2.2GB`，5年即为 11GB。
* 用于事件数据的 Housekeeper 配置：每个 Zabbix event 需要大概170字节的空间。但每天的 event 数量比较难以估计，考虑最坏的情况，每秒产生一个 event ，则3年的数据量为`3*365*24*3600* 170 = 15GB`。

所以磁盘所需的容量的计算公式如下：

```
配置 + 历史数据 + 趋势数据 + 事件数据
```

#### 时间同步

对于 Zabbix 的运行，要求时间同步正确，你可以使用 [ntp](http://www.ntp.org/) 来确保你的服务器时间正确。


### 2. 从包管理器中安装

常见的操作系统都提供了 Zabbix 包的下载，可以通过包管理器来安装 Zabbix。

Zabbix SIA 提供了官方 RPM 和 DEB 包，可以用于 Red Hat Enterprise Linux、Debian 和 Ubuntu LTS。

包文件可在 <http://repo.zabbix.com/> 找到。 


#### Red Hat Enterprise Linux / CentOS

支持 RHEL 7、Oracle Linux 7、CentOS 7 等版本的平台，一些包（agent、proxy 等）也适用于 RHEL 5、RHEL 6。

（1）配置 yum  的库


配置 yum  的库方式如下：

```
# rpm -ivh http://repo.zabbix.com/zabbix/3.2/rhel/7/x86_64/zabbix-release-3.2-1.el7.noarch.rpm
```

（2）安装 Zabbix 包

下面安装 mysql 数据版本的 Zabbix server 和 web 前端的例子：

```
# yum install zabbix-server-mysql zabbix-web-mysql
```

下面安装  Zabbix agent 的例子：

```
# yum install zabbix-agent
```

（3）创建初始化数据库

在 MySQL 上创建数据库和用户（读者可以自行参阅 <https://www.zabbix.com/documentation/3.2/manual/appendix/install/db_scripts>），而后导入初始化的模式和数据：

```
# cd /usr/share/doc/zabbix-server-mysql-3.2.0
# zcat create.sql.gz | mysql -uroot zabbix
```

（4）启动  Zabbix server 进程

编辑`zabbix_server.conf`：


```
# vi /etc/zabbix/zabbix_server.conf
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=zabbix
```

启动  Zabbix server 进程：

```
# systemctl start zabbix-server
```

（5）编辑 PHP 配置

Zabbix 前端的 Apache 文件位于`/etc/httpd/conf.d/zabbix.conf`。一些 PHP 设置已经配置好了：

```
php_value max_execution_time 300
php_value memory_limit 128M
php_value post_max_size 16M
php_value upload_max_filesize 2M
php_value max_input_time 300
php_value always_populate_raw_post_data -1
# php_value date.timezone Europe/Riga
```

取消对“date.timezone”设置是必要的，并设置正确的时区。更改配置文件后重新启动 Apache Web 服务器。

```
# systemctl start httpd
```

启动后就能在浏览器访问 <http://zabbix-frontend-hostname/zabbix>
（zabbix-frontend-hostname 是你的主机名称），其默认用户名密码是 
 Admin/zabbix。



#### Debian / Ubuntu

支持 Debian 7（Wheezy）和 8（Jessie）、Ubuntu 14.04 LTS（Trusty Tahr）、16.04 LTS（Xenial Xerus）等版本的平台 。

（1）配置包的库


配置包的库的方式如下，Debian 7：

```
# wget http://repo.zabbix.com/zabbix/3.2/debian/pool/main/z/zabbix-release/zabbix-release_3.2-1+wheezy_all.deb
# dpkg -i zabbix-release_3.2-1+wheezy_all.deb
# apt-get update
```

Debian 8：

```
# wget http://repo.zabbix.com/zabbix/3.2/debian/pool/main/z/zabbix-release/zabbix-release_3.2-1+jessie_all.deb
# dpkg -i zabbix-release_3.2-1+jessie_all.deb
# apt-get update
```


Ubuntu 14.04 LTS：

```
# wget http://repo.zabbix.com/zabbix/3.2/ubuntu/pool/main/z/zabbix-release/zabbix-release_3.2-1+trusty_all.deb
# dpkg -i zabbix-release_3.2-1+trusty_all.deb
# apt-get update
```

Ubuntu 16.04 LTS：

```
# wget http://repo.zabbix.com/zabbix/3.2/ubuntu/pool/main/z/zabbix-release/zabbix-release_3.2-1+xenial_all.deb
# dpkg -i zabbix-release_3.2-1+xenial_all.deb
# apt-get update
```


（2）安装 Zabbix 包

下面安装 mysql 数据版本的 Zabbix server 和 web 前端的例子：

```
# apt-get install zabbix-server-mysql zabbix-frontend-php
```

下面安装  Zabbix agent 的例子：

```
# apt-get install zabbix-agent
```

（3）创建初始化数据库

在 MySQL 上创建数据库和用户（读者可以自行参阅 <https://www.zabbix.com/documentation/3.2/manual/appendix/install/db_scripts>），而后导入初始化的模式和数据：

```
# cd /usr/share/doc/zabbix-server-mysql
# zcat create.sql.gz | mysql -uroot zabbix
```

（4）启动  Zabbix server 进程

编辑`zabbix_server.conf`：


```
# vi /etc/zabbix/zabbix_server.conf
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=zabbix
```

启动  Zabbix server 进程：

```
# service zabbix-server start
```

（5）编辑 PHP 配置

Zabbix 前端的 Apache 文件位于`/etc/apache2/conf.d/zabbix`或`/etc/apache2/conf-enabled/zabbix.conf`。一些 PHP 设置已经配置好了：

```
php_value max_execution_time 300
php_value memory_limit 128M
php_value post_max_size 16M
php_value upload_max_filesize 2M
php_value max_input_time 300
php_value always_populate_raw_post_data -1
# php_value date.timezone Europe/Riga
```

取消对“date.timezone”设置是必要的，并设置正确的时区。更改配置文件后重新启动 Apache Web 服务器。

```
# service apache2 restart
```

启动后就能在浏览器访问 <http://zabbix-frontend-hostname/zabbix>
（zabbix-frontend-hostname 是你的主机名称），其默认用户名密码是 
 Admin/zabbix。
 
 
### 3. 从源码中安装

下面介绍从源码安装 Zabbix 的步骤。

#### 安装 Zabbix 守护进程

（1）下载源码包

从 <http://www.zabbix.com/download.php> 下载源码，并解压：


```
$ tar -zxvf zabbix-3.2.0.tar.gz
```

（2）创建用户账号

对于所有的 Zabbix 守护进程中，需要非特权用户。如果的 Zabbix 守护程序从一个非特权用户帐户启动，它将作为该用户运行。

但是，如果一个守护进程从“root”帐户启动，它会切换到“zabbix”用户帐户，该帐户必须存在。在 Linux 系统上创建这样的用户帐户（在其自己的“zabbix”组）：


```
groupadd zabbix
useradd -g zabbix zabbix
```

Zabbix 前端安装时不需要一个单独的用户帐户。

如果 Zabbix server 和 agent 在同一台机器上运行，建议使用不同的用户来运行 server 和 agent 。否则，如果两者都为同一用户运行，该 agent 可以访问 server 的配置文件，可以很容易获取到 Admin 级别的用户的资料，例如，数据库口令。

（3）创建 Zabbix 数据库

对于 Zabbix server 和 proxy 守护进程、Zabbix 前端，数据库是必须。运行 Zabbix agent 则不需要创建 Zabbix 数据库。

可以参考 <https://www.zabbix.com/documentation/3.2/manual/appendix/install/db_scripts> 执行 SQL 脚本来创建 Zabbix 数据库。

（4）配置源码

当配置 Zabbix server 或 proxy 的源码时，必须指定要使用的数据库类型。一个 server 或 proxy 在同一个时间只有一个数据库类型可以被编译。

查看配置的详细选项，可以执行：

```
./configure --help
```

配置 Zabbix server 或 agent 的源码，执行如下：

```
./configure --enable-server --enable-agent --with-mysql --enable-ipv6 --with-net-snmp --with-libcurl --with-libxml2
```

配置 Zabbix server（使用 PostgreSQL ）的源码，可以执行如下：

```
./configure --enable-server --with-postgresql --with-net-snmp
```


配置 Zabbix proxy （使用 SQLite 等）的源码，可以执行如下：

```
./configure --prefix=/usr --enable-proxy --with-net-snmp --with-sqlite3 --with-ssh2
```


配置 Zabbix agent 的源码，可以执行如下：

```
./configure --enable-agent
```

（5）执行 make install


若是从 SVN 进行安装，则需要在  make install 前先执行：

```
make dbschema
```

拥有足够的权限的用户（一般是“root”或“sudo”）来执行安装：

```
make install
```

默认安装位置，守护进程的编译文件（zabbix_server、zabbix_agentd、zabbix_proxy） 是在 `/usr/local/sbin`，而客户端的编译文件（zabbix_get、zabbix_sender） 是在 `/usr/local/bin`。


（6）修改配置文件

* 修改 Zabbix agent 配置文件 `/usr/local/etc/zabbix_agentd.conf`，需要修改每个 host 的 zabbix_agentd。指定 Zabbix server 的 IP 地址。
* 修改 Zabbix server 配置文件 `/usr/local/etc/zabbix_server.conf`，需要指定数据库名称、用户名和密码。
* 若安装了  Zabbix proxy，修改 proxy 配置文件 `/usr/local/etc/zabbix_proxy.conf`，需指定 server 的 IP 地址和 proxy 的主机名称，以及数据库名称、用户名和密码。


（7）启动守护进程

运行服务器端的 zabbix_server，执行如下： 

```
shell> zabbix_server
```

运行被监控机器的 zabbix_agentd，执行如下： 

```
shell> zabbix_agentd
```

若安装了 Zabbix proxy，运行 zabbix_proxy，执行如下： 

```
shell> zabbix_proxy
```


### 安装 Zabbix web 界面

Zabbix 的前端是用 PHP 编写的，所以安装过程比较简单，只需把 PHP 文件从 `frontends/php` 拷贝到 web 服务器的 HTML 目录。

Apache web 服务器的 HTML 文档目录路径位置一般包括：

* /usr/local/apache2/htdocs（Apache 默认安装位置）
* /srv/www/htdocs（OpenSUSE、SLES）
* /var/www/html（Fedora、RHEL、CentOS）
* /var/www（Debian、Ubuntu）

建议在 HTML 的根目录下创建子目录。执行如下：

```
mkdir <htdocs>/zabbix
cd frontends/php
cp -a . <htdocs>/zabbix
```

如果从 SVN 来进行安装，并考虑非英文的语言使用，需生产翻译文件，执行如下：

```
locale/make_mo.sh
```

下面是安装前端的步骤：

（1）在浏览器打开 Zabbix URL `http://<server_ip_or_name>/zabbix`，可以看到如下界面：

![图6-7 安装步骤1](https://www.zabbix.com/documentation/3.2/_media/manual/installation/install_1.png?w=550&tok=fd0e5e)

图6-7 安装步骤1


（2）确保软件的所有前置条件都满足了：

![图6-8 安装步骤2](https://www.zabbix.com/documentation/3.2/_media/manual/installation/install_2.png?w=550&tok=0b5ea8)

图6-8 安装步骤2

（3）确保数据库已经创建完毕，而后输入数据库的连接详情：

![图6-9 安装步骤3](https://www.zabbix.com/documentation/3.2/_media/manual/installation/install_3.png?w=550&tok=65e211)

图6-9 安装步骤3

（4）输入  Zabbix server 详情：

![图6-10 安装步骤4](https://www.zabbix.com/documentation/3.2/_media/manual/installation/install_4.png?w=550&tok=b0e91d)

图6-10 安装步骤4


（5）复查配置总览：

![图6-11 安装步骤5](https://www.zabbix.com/documentation/3.2/_media/manual/installation/install_5.png?w=550&tok=91477d)

图6-11 安装步骤5


（6）下载配置文件来替换 web 服务器 HTML 文档子目录`conf/`下的配置文件：

![图6-12 安装步骤6](https://www.zabbix.com/documentation/3.2/_media/manual/installation/install_6.png?w=550&tok=4664a0)

图6-12 安装步骤6

（7）完成安装：

![图6-13 安装步骤7](https://www.zabbix.com/documentation/3.2/_media/manual/installation/install_7.png?w=550&tok=0d699c)


图6-13 安装步骤7


（8）安装完成后能通过以下登录界面进行登录，默认用户账号密码为 Admin/zabbix：


![图6-14 安装步骤8](https://www.zabbix.com/documentation/3.2/_media/manual/quickstart/login.png?w=350&tok=128699)


图6-14 安装步骤8



### 4. 从容器中安装

目前，支持 Docker 镜像形式的容器来安装 Zabbix。Zabbix 的官方 Docker 文件地址在 <https://github.com/zabbix/zabbix-docker>，相关组件的镜像文件，也可以从 Docker 库中获得：

* Zabbix agent：<https://hub.docker.com/r/zabbix/zabbix-agent/>
* Zabbix server
  * MySQL 版本：<https://hub.docker.com/r/zabbix/zabbix-server-mysql/>
  * PostgreSQL 版本：<https://hub.docker.com/r/zabbix/zabbix-server-mysql/>
* Zabbix web 界面
  * Apache2 web 服务器、MySQL 版本：<https://hub.docker.com/r/zabbix/zabbix-web-apache-mysql/>
  * Nginx web 服务器、MySQL 版本：<https://hub.docker.com/r/zabbix/zabbix-web-nginx-mysql/>
  *  Nginx web 服务器、PostgreSQL 版本：<https://hub.docker.com/r/zabbix/zabbix-web-nginx-pgsql/>
* Zabbix proxy
  * SQLite3 版本：<https://hub.docker.com/r/zabbix/zabbix-proxy-sqlite3/>
  * MySQL 版本：<https://hub.docker.com/r/zabbix/zabbix-proxy-mysql/>
* Zabbix Java Gateway：<https://hub.docker.com/r/zabbix/zabbix-java-gateway/>


#### 示例1

本示例演示如何运行 MySQL 版本的 Zabbix server，其 Zabbix web 界面基于 Nginx web 服务器和 Zabbix Java gateway。

（1）开启一个空的 MySQL server 实例：

```
# docker run --name mysql-server -t \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix_pwd" \
      -e MYSQL_ROOT_PASSWORD="root_pwd" \
      -d mysql:5.7
```

（2）开启一个 Zabbix Java gateway 实例：

```
# docker run --name zabbix-java-gateway -t \
      -d zabbix/zabbix-java-gateway:latest
```

（3）开启 Zabbix server 实例，并链接到创建的 MySQL server 实例：

```
# docker run --name zabbix-server-mysql -t \
      -e DB_SERVER_HOST="mysql-server" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix_pwd" \
      -e MYSQL_ROOT_PASSWORD="root_pwd" \
      -e ZBX_JAVAGATEWAY="zabbix-java-gateway" \
      --link mysql-server:mysql \
      --link zabbix-java-gateway:zabbix-java-gateway \
      -p 10051:10051 \
      -d zabbix/zabbix-server-mysql:latest
```


（4）开启 Zabbix web 实例，并链接到创建的 MySQL server 和 Zabbix server 实例：

```
# docker run --name zabbix-web-nginx-mysql -t \
      -e DB_SERVER_HOST="mysql-server" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix_pwd" \
      -e MYSQL_ROOT_PASSWORD="root_pwd" \
      --link mysql-server:mysql \
      --link zabbix-server-mysql:zabbix-server \
      -p 80:80 \
      -d zabbix/zabbix-web-nginx-mysql:latest
```


#### 示例2

本示例演示如何运行 PostgreSQL 版本的 Zabbix server，其 Zabbix web 界面基于 Nginx web 服务器和 SNMP trap。

（1）开启一个空的 PostgreSQL server 实例：

```
# docker run --name postgres-server -t \
      -e POSTGRES_USER="zabbix" \
      -e POSTGRES_PASSWORD="zabbix" \
      -e POSTGRES_DB="zabbix_pwd" \
      -d postgres:latest
```

（2）开启一个 Zabbix snmptraps 实例：

```
# docker run --name zabbix-snmptraps -t \
      -v ./zbx_instance/snmptraps:/var/lib/zabbix/snmptraps:rw \
      -v /var/lib/zabbix/mibs:/usr/share/snmp/mibs:ro \
      -p 162:162/udp \
      -d zabbix/zabbix-snmptraps:latest
```

（3）开启 Zabbix server 实例，并链接到创建的 PostgreSQL server 实例：

```
# docker run --name zabbix-server-mysql -t \
      -e DB_SERVER_HOST="postgres-server" \
      -e POSTGRES_USER="zabbix" \
      -e POSTGRES_PASSWORD="zabbix" \
      -e POSTGRES_DB="zabbix_pwd" \
      -e ZBX_ENABLE_SNMP_TRAPS="true" \
      --link postgres-server:postgres \
      -p 10051:10051 \
      --volumes-from zabbix-snmptraps \
      -d zabbix/zabbix-server-mysql:latest
```


（4）开启 Zabbix web 实例，并链接到创建的 PostgreSQL server 和 Zabbix server 实例：

```
# docker run --name zabbix-web-nginx-pgsql -t \
      -e DB_SERVER_HOST="postgres-server" \
      -e POSTGRES_USER="zabbix" \
      -e POSTGRES_PASSWORD="zabbix" \
      -e POSTGRES_DB="zabbix_pwd" \
      --link postgres-server:postgres \
      --link zabbix-server-mysql:zabbix-server \
      -p 443:443 \
      -v /etc/ssl/nginx:/etc/ssl/nginx:ro \
      -d zabbix/zabbix-web-nginx-pgsql:latest
```



## 参考引用

* <https://www.zabbix.com/documentation/3.2/manual/installation>
* 《分布式系统常用技术及案例分析》:<https://github.com/waylau/distributed-systems-technologies-and-cases-analysis>