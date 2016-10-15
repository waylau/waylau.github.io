---
layout: post
title: Nagios 在不同平台下的安装
date: 2016-10-15 01:41
author: admin
comments: true
categories: [Nagios]
tags: [Nagios]
---
 
Nagios 是一款开源的免费网络监视工具，致力于打造符合行业标准的 IT 基础架构的监控系统。Nagios 提供了服务器、网络和应用的完整的 IT 监控和报警，可以有效监控 Windows、Linux 和 Unix 的主机状态，以及交换机、路由器、打印机等网络设备。在系统或服务状态异常时可以发出邮件或短信报警第一时间通知网站运维人员，在状态恢复后发出正常的邮件或短信进行通知。

本节介绍了 Nagios 在不同平台上的安装和使用过程。
<!-- more -->

截止目前 Nagios Core 的最新版本为 4.2.1。Nagios 官方网站为 <https://www.nagios.com>。

### 1. 安装要求

Nagios 所需要的运行条件是机器必须可以运行 Linux（或是 Unix 变种）并且装有 C 语言编译器（如果是从源码进行安装的话）。你必须正确地配置 TCP/IP 协议栈以使大多数的服务检测可以通过网络得以进行。

Nagios 里的 CGI 程序不是必须的，如果你要使用 CGI 程序时，你必须要安装以下这些软件：

* 一个 Web 服务，最好是 Apache；
* Thomas Boutell 制作的 [gd 库](https://boutell.com/gd/)版本应是1.6.3或更高（在 CGI 程序模块 [statusmap](https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/4/en/cgis.html#statusmap_cgi) 和 [trends](https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/4/en/cgis.html#trends_cgi) 这两个模块里需要这个库）

### 2. Fedora 平台下的安装

下面介绍下在 Fedora 平台下，从源码进行 Nagios 安装的步骤。本例使用的是 Fedora Core 6 版本。

如果安装成功，最后将得到如下的结果：

* Nagios 和插件将安装到 `/usr/local/nagios`；
* Nagios 将被配置为监控本地系统的几个主要服务（CPU 负荷、磁盘利用率等）；
* Nagios 的 Web 界面的 URL 是 <http://localhost/nagios/>。

（1）安装条件

首先，需要拥有 root 用户权限来访问你的机器。其次，确保你的机器中已经安装了下面这些软件包：

* Apache
* PHP
* GCC 编译器
* GD 开发包

可以使用 yum 来安装上面这些包（使用 root 账号）：

```
yum install httpd php
yum install gcc glibc glibc-common
yum install gd gd-devel
```


（2）创建账号信息

切换为 root 用户：

```
su -l
```


创建一个名为 nagios 的帐号并设定登录口令：


```
/usr/sbin/useradd -m nagios
passwd nagios
```

创建一个用户组名为 nagcmd 用于从 Web 界面执行外部命令。将 nagios 用户和 apache 用户都加到这个组中：



```
/usr/sbin/groupadd nagcmd
/usr/sbin/usermod -a -G nagcmd nagios
/usr/sbin/usermod -a -G nagcmd apache
```


（3）下载 Nagios 和插件程序包

建立一个目录用以存储下载文件：

```
mkdir ~/downloads
cd ~/downloads
```


下载 Nagios 和 Nagios 插件的软件包。访问 <https://www.nagios.org/download> 站点以获得最新版本：


```
wget http://prdownloads.sourceforge.net/sourceforge/nagios/nagios-4.0.8.tar.gz
wget http://www.nagios-plugins.org/download/nagios-plugins-2.0.3.tar.gz
```

（4）编译与安装 Nagios

解压 Nagios 源程序包：

```
cd ~/downloads
tar xzf nagios-4.0.8.tar.gz
cd nagios-4.0.8
```

运行 Nagios 配置脚本，并使用先前开设的用户及用户组：

```
./configure --with-command-group=nagcmd
```

编译 Nagios 程序包源码：

```
make all
```

安装二进制运行程序、初始化脚本、配置文件样本并设置运行目录权限：

```
make install
make install-init
make install-config
make install-commandmode
```
 
（5）自定义配置

样例配置文件默认安装在这个 `/usr/local/nagios/etc` 目录下，这些样例文件可以只需要做一个简单的修改，便可以配置 Nagios 使之正常运行。

编辑这个 `/usr/local/nagios/etc/objects/contacts.cfg` 配置文件，更改 email 地址来关联 nagiosadmin 联系人定义 email，这样该 email 就能接收到报警内容。

```
vi /usr/local/nagios/etc/objects/contacts.cfg
```

（6）配置 Web 界面

安装 Nagios 的 Web 配置文件到 Apache 的 `conf.d` 目录下：

```
make install-webconf
```


创建一个 nagiosadmin 的用户用于登录 Nagios 的 Web 界面。记下你所设置的登录口令，一会儿你会用到它：

```
htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
```

重启 Apache 服务以使设置生效：


```
service httpd restart
```

（7）编译并安装 Nagios 插件

解压 Nagios 插件的源程序包：


```
cd ~/downloads
tar xzf nagios-plugins-2.0.3.tar.gz
cd nagios-plugins-2.0.3
```

编译并安装插件：

```
./configure --with-nagios-user=nagios --with-nagios-group=nagios
make
make install
```

（8）启动 Nagios

把 Nagios 加入到服务列表中以使之在系统启动时自动启动：

```
chkconfig --add nagios
chkconfig nagios on
```

验证 Nagios 的样例配置文件：

```
/usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
```

如果没有报错，可以启动 Nagios 服务：



```
service nagios start
```


（9）更改 SELinux 设置

Fedora 与 SELinux（安全增强型 Linux）同步发行，安装后会默认使用强制（Enforcing）模式。这会在你尝试联入 Nagios 的 CGI 时导致一个“内部服务错误”消息。

如果是 SELinux 处于强制安全模式时需要做：


```
getenforce
```


令 SELinux 处于容许（Permissive）模式：

```
setenforce 0
```

如果要永久性更变它，需要更改`/etc/selinux/config`里的设置并重启系统。

不关闭 SELinux 或是永久性变更它的方法是让 CGI 模块在 SELinux 下指定强制目标模式：

```
chcon -R -t httpd_sys_content_t /usr/local/nagios/sbin/
chcon -R -t httpd_sys_content_t /usr/local/nagios/share/
```


（10）登录到 Web 界面

你现在可以从 URL 来访问 Web 界面了，你需要在提示下输入你的用户名（nagiosadmin）和你刚刚设置的口令。URL 地址为 <http://localhost/nagios/>


点击“Service Detail（服务详情）”的引导超链来查看你本机的监视详情。你可能需要给点时间让 Nagios 来检测你机器上所依赖的服务，因为检测需要些时间。

（11）其他设置

确保你机器的防火墙规则配置允许你可以从远程登录到 Nagios 的 Web 服务器。



### 3. openSUSE 平台下的安装


下面介绍下在 openSUSE 平台下，从源码进行 Nagios 安装的步骤。本例使用的是 openSUSE 10.2 版本。

（1）安装条件

确保你的机器中已经安装了下面这些软件包：

* apache2
* C/C++ 开发包 

可以使用 yast 来安装上面这些包。
 

（2）创建账号信息

切换为 root 用户：

```
su -l
```


创建一个名为 nagios 的帐号并设定登录口令：


```
/usr/sbin/useradd -m nagios
passwd nagios
```

创建一个用户组名为 nagios。将 nagios 用户都加到这个组中：

```
/usr/sbin/useradd -m nagios
passwd nagios
```


创建一个用户组名为 nagcmd 用于从 Web 界面执行外部命令。将 nagios 用户和 apache 用户都加到这个组中：

```
/usr/sbin/groupadd nagcmd
/usr/sbin/usermod -a -G nagcmd nagios
/usr/sbin/usermod -a -G nagcmd wwwrun
```


（3）下载 Nagios 和插件程序包

建立一个目录用以存储下载文件：

```
mkdir ~/downloads
cd ~/downloads
```

下载 Nagios 和 Nagios 插件的软件包。访问 <https://www.nagios.org/download> 站点以获得最新版本。


```
wget http://prdownloads.sourceforge.net/sourceforge/nagios/nagios-4.0.8.tar.gz
wget http://www.nagios-plugins.org/download/nagios-plugins-2.0.3.tar.gz
```

（4）编译与安装 Nagios

解压 Nagios 源程序包

```
cd ~/downloads
tar xzf nagios-4.0.8.tar.gz
cd nagios-4.0.8
```

运行 Nagios 配置脚本，并使用先前开设的用户及用户组：

```
./configure --with-command-group=nagcmd
```

编译 Nagios 程序包源码：

```
make all
```

安装二进制运行程序、初始化脚本、配置文件样本并设置运行目录权限：

```
make install
make install-init
make install-config
make install-commandmode
```
 
（5）自定义配置

样例配置文件默认安装在这个 `/usr/local/nagios/etc` 目录下，这些样例文件可以配置 Nagios 使之正常运行，只需要做一个简单的修改。

编辑这个 `/usr/local/nagios/etc/objects/contacts.cfg` 配置文件，更改 email 地址来关联 nagiosadmin 联系人定义 email，这样该 email 就能接收到报警内容。

```
vi /usr/local/nagios/etc/objects/contacts.cfg
```

（6）配置 Web 界面

安装 Nagios 的 Web 配置文件到 Apache 的 `conf.d` 目录下：

```
make install-webconf
```


创建一个 nagiosadmin 的用户用于登录 Nagios 的 Web 界面。记下你所设置的登录口令，一会儿你会用到它。

```
htpasswd2 -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
```

重启 Apache 服务以使设置生效：


```
service apache2 restart
```

（7）编译并安装 Nagios 插件

解压 Nagios 插件的源程序包：


```
cd ~/downloads
tar xzf nagios-plugins-2.0.3.tar.gz
cd nagios-plugins-2.0.3
```

编译并安装插件：

```
./configure --with-nagios-user=nagios --with-nagios-group=nagios
make
make install
```

（8）启动 Nagios

把 Nagios 加入到服务列表中以使之在系统启动时自动启动：

```
chkconfig --add nagios
chkconfig nagios on
```

验证 Nagios 的样例配置文件：

```
/usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
```

如果没有报错，可以启动 Nagios 服务：



```
service nagios start
```


（9）登录到 Web 界面

你现在可以从 URL 来访问 Web 界面了，你需要在提示下输入你的用户名（nagiosadmin）和你刚刚设置的口令。URL 地址为 <http://localhost/nagios/>。

点击“Service Detail（服务详情）”的引导超链来查看你本机的监视详情。你可能需要给点时间让 Nagios 来检测你机器上所依赖的服务，因为检测需要些时间。

（10）其他设置

确保你机器的防火墙规则配置允许你可以从远程登录到 Nagios 的 Web 服务器。你可以这样做：

* 打开控制中心；
* 选择“Open Administrator Settings（打开超户设置）”以打开 YaST 用户控制中心；
* 选择在“Security and Users（安全与用户）”分类下的“Firewall（防火墙）”；
* 在防火墙的配置窗口中点击“Allowed Services（允许的服务）”选项；
* 在“External Zone（外部区）”允许的服务中添加“'HTTP Server（HTTP 服务器）”；
* 点击“Next（下一步）”并选择“Accept（接受）”以使得防火墙设置生效。

### 4. Ubuntu 平台下的安装


下面介绍下在 Ubuntu 平台下，从源码进行 Nagios 安装的步骤。本例使用的是  Ubuntu 6.10（desktop）版本。

如果安装成功，最后将得到如下的结果：

* Nagios 和插件将安装到 `/usr/local/nagios`；
* Nagios 将被配置为监控本地系统的几个主要服务（CPU 负荷、磁盘利用率等）；
* Nagios 的 Web 界面的 URL 是 <http://localhost/nagios/>。

（1）安装条件

确保你的机器中已经安装了下面这些软件包：

* Apache 2
* PHP
* GCC 编译器
* GD 开发包

可以使用 apt-get 来安装上面这些包：

```
sudo apt-get install apache2
sudo apt-get install libapache2-mod-php5
sudo apt-get install build-essential
```

在 Ubuntu 6.10 版本，安装 gd2 库使用下面的命令：

```
sudo apt-get install libgd2-dev
```

在 Ubuntu 7.10 版本，安装 gd2 库的名称更改了，所以使用的命令有所不同：

```
sudo apt-get install libgd2-xpm-dev
```



（2）创建账号信息

切换为 root 用户：

```
sudo -s
```


创建一个名为 nagios 的帐号并设定登录口令：


```
/usr/sbin/useradd -m -s /bin/bash nagios
passwd nagios
```


在老版本的 Ubuntu 服务器版本中（6.01 及之前的版本），需要添加 nagios 组中。桌面版本或者 6.01 之后的服务器版本，则不需要下面的步骤：

```
/usr/sbin/groupadd nagios
/usr/sbin/usermod -G nagios nagios
```



创建一个用户组名为 nagcmd 用于从 Web 界面执行外部命令。将 nagios 用户和 apache 用户都加到这个组中：



```
/usr/sbin/groupadd nagcmd
/usr/sbin/usermod -a -G nagcmd nagios
/usr/sbin/usermod -a -G nagcmd www-data
```


（3）下载 Nagios 和插件程序包

建立一个目录用以存储下载文件：

```
mkdir ~/downloads
cd ~/downloads
```


下载 Nagios 和 Nagios 插件的软件包。访问 <https://www.nagios.org/download> 站点以获得最新版本：


```
wget http://prdownloads.sourceforge.net/sourceforge/nagios/nagios-4.0.8.tar.gz
wget http://www.nagios-plugins.org/download/nagios-plugins-2.0.3.tar.gz
```

（4）编译与安装 Nagios

解压 Nagios 源程序包：

```
cd ~/downloads
tar xzf nagios-4.0.8.tar.gz
cd nagios-4.0.8
```

运行 Nagios 配置脚本，并使用先前开设的用户及用户组：

```
./configure --with-command-group=nagcmd
```

编译 Nagios 程序包源码：

```
make all
```

安装二进制运行程序、初始化脚本、配置文件样本并设置运行目录权限：

```
make install
make install-init
make install-config
make install-commandmode
```
 
（5）自定义配置

样例配置文件默认安装在这个 `/usr/local/nagios/etc` 目录下，这些样例文件可以配置 Nagios 使之正常运行，只需要做一个简单的修改。

编辑这个 `/usr/local/nagios/etc/objects/contacts.cfg` 配置文件，更改 email 地址来关联 nagiosadmin 联系人定义 email，这样该 email 就能接收到报警内容。

```
vi /usr/local/nagios/etc/objects/contacts.cfg
```

（6）配置 Web 界面

安装 Nagios 的 Web 配置文件到 Apache 的 `conf.d` 目录下：

```
make install-webconf
```


创建一个 nagiosadmin 的用户用于登录 Nagios 的 Web 界面。记下你所设置的登录口令，一会儿你会用到它：

```
htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
```

重启 Apache 服务以使设置生效：


```
/etc/init.d/apache2 reload
```

（7）编译并安装 Nagios 插件

解压 Nagios 插件的源程序包：


```
cd ~/downloads
tar xzf nagios-plugins-2.0.3.tar.gz
cd nagios-plugins-2.0.3
```

编译并安装插件：

```
./configure --with-nagios-user=nagios --with-nagios-group=nagios
make
make install
```

（8）启动 Nagios

把 Nagios 加入到服务列表中以使之在系统启动时自动启动：

```
ln -s /etc/init.d/nagios /etc/rcS.d/S99nagios
```

验证 Nagios 的样例配置文件：

```
/usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
```

如果没有报错，可以启动 Nagios 服务：



```
/etc/init.d/nagios start
```


（9）登录到 Web 界面

你现在可以从 URL 来访问 Web 界面了，你需要在提示下输入你的用户名（nagiosadmin）和你刚刚设置的口令。URL 地址为 <http://localhost/nagios/>


点击“Service Detail（服务详情）”的引导超链来查看你本机的监视详情。你可能需要给点时间让 Nagios 来检测你机器上所依赖的服务，因为检测需要些时间。

（10）其他设置


如果要接收 Nagios 的 email 警报，需要安装 mailx（Postfix）包：

```
sudo apt-get install mailx
sudo apt-get install postfix
```


需要编辑 Nagios 里的 email 通知送出命令，它位于`/usr/local/nagios/etc/objects/commands.cfg`文件中，将里面的`/bin/mail`全部替换为`/usr/bin/mail`。一旦设置好需要重启动 Nagios 以使配置生效。

```
sudo /etc/init.d/nagios restart
```


## 参考引用

* <https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/4/en/quickstart.html>
* 《分布式系统常用技术及案例分析》:<https://github.com/waylau/distributed-systems-technologies-and-cases-analysis>