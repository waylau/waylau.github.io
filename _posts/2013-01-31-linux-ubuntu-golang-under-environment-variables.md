---
layout: post
title:  Linux(Ubuntu)下设置golang环境变量
date:  2013-01-31 00:38
author: admin
comments: true
categories: [Golang]
tags: [Golang,Linux]
---

1).源代码安装go语言

Go的工具链采用C语言编写，要构建它，你需要安装一个C编译器，本例采用gcc工具; 进入linux 终端，输入命令行

>sudo apt-get install build-essential
 

Go使用Mercurial进行版本管理，首先你必须安装了Mercurial,对于 Ubuntu/Debian 系统先安装easy_install;

>sudo apt-get install mercurial
 

出现Processing triggers for python-support...需先安装python环境 执行
>
sudo apt-get install python-setuptools python-dev
>
sudo easy_install mercurial
 

如果 还不行，那直接下载Mercurial安装

获取GO代码

>hg clone -r release https://go.googlecode.com/hg/ $GOROOT
 

安装Go:

>cd $GOROOT/src
>
./all.bash
 

如果 还不行，那直接下载编译好的代码安装 解压 go包放用户根目录下

>sudo tar zxvf ~/Downloads/go1.0.3.linux-386.tar.gz -C /opt
 

2).设置环境变量,在/etc/profile文件同添加以下内容

	sudo gedit /etc/profile
	
	export GOROOT=$HOME/go
	export GOBIN=$GOROOT/bin
	export GOARCH=386
	export GOOS=linux
	export GOPATH=$HOME/workspacego
	export PATH=.:$PATH:$GOBIN
 

GOARCH、GOOS为可选配置，交叉编译时有用 GOPATH设置工作空间,允许多个目录，当有多个目录时，用分隔符分隔，当有多个GOPATH时，默认会将go get的内容放在第一个目录下;

$GOPATH 目录约定有三个子目录：

src 存放源代码（比如：.go .c .h .s等）

pkg 编译后生成的文件（比如：.a）

bin 编译后生成的可执行文件（为了方便，可以把此目录加入到 $PATH 变量中）

应用刚刚配置的环境变量:source /etc/profile

上面的配置是针对系统级别的，建议采用用户级别的配置如下：

如果用户根目录下不存在.bash_profile，则

	cat > .bash_profile
	
	export GOROOT=$HOME/go
	export PATH=$PATH:$GOROOT/bin
	export GOPATH=$HOME/workspacego
 

CTRL_D(这是按 CTRL - D两个键同时)

每次启动系统应用

>. ./.bash_profile
 

官方建议把配置记录在用户在～/.bashrc或者 ～/.profile中，这样 登陆就可以使用go 而不用，每次登陆系统都要执行. ./.bash_profile

配置文件的区别，请参阅后面的附录

3).查看go是否是release版

>hg identify
 

更新go到新版本

	cd $GOROOT
	
	hg pull
	
	hg update release
	
	./all.bash
 

4).浏览本地doc

在命令行执行
>godoc -http=:8080

在浏览器地址输入：<http://127.0.0.1:8080>，即可进入doc界面

5).搭建基于浏览器的交互式Go编程指南-gotour

5.1).安装Go语言英文教程： 
>sudo go get code.google.com/p/go-tour/gotour

5.2).安装Go语言中文教程： 
>sudo go get bitbucket.org/mikespook/go-tour-zh/gotour

在命令行执行gotour，在浏览器地址输入：http://127.0.0.1:3999，即可进入教程的学习界面。

附录

参考：<http://blog.csdn.net/wumingxing0228/article/details/6050175>

环境变量是和Shell紧密相关的，用户登录系统后就启动了一个Shell。对于Linux来说一般是bash，但也可以重新设定或切换到其它的 Shell。对于UNIX，可能是CShelll。环境变量是通过Shell命令来设置的，设置好的环境变量又可以被所有当前用户所运行的程序所使用。对于bash这个Shell程序来说，可以通过变量名来访问相应的环境变量，通过export来设置环境变量。下面通过几个实例来说明。

1)etc/profile:此文件为系统的每个用户设置环境信息,当用户第一次登录时,该文件被执行. 并从/etc/profile.d目录的配置文件中搜集shell的设置. 注：在这里我们设定是为所有用户可使用的全局变量。

2)/etc/bashrc:为每一个运行bash shell的用户执行此文件.当bash shell被打开时,该文件被读取.

3)~/.bashprofile:每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件. 注：~在LINUX下面是代表HOME这个变量的。 另外在不同的LINUX操作系统下，这个文件可能是不同的，可能是~/.bashprofile； ~/.bash_login或 ~/.profile其中的一种或几种，如果存在几种的话，那么执行的顺序便是：~/.bashprofile、 ~/.bashlogin、 ~/.profile。比如我用的是Ubuntu，我的用户文件夹下默认的就只有~/.profile文件。

4)~/.bashrc:该文件包含专用于你的bash shell的bash信息,当登录时以及每次打开新的shell时,该文件被读取. (注：这个文件是 .开头的，所以在文件夹中被隐藏了) 那么我们如何添加自己定义的环境变量呢？ 用记事本打开这个文件，然后在里面最后写上: xiaokang=kangkang 然后保存，这样每次打开一个新的terminal的时候，我们这个变量就生效了。记住，如果你已经打开一个terminal，然后你修改了这个文件，那么在这个terminal下是不会生效的。一般情况用户最好在这里进行修改，但是有时候会覆盖父级的变量，比如PATH是ROOT设定的，但是如果你在这个文件里面写了PATH=xx,那么将来所有的PATH都成了xx了，所以我们应该在这个文件中写为： PATH＝$PATH:xx 这样就把原来的和你自己的一起加上了。而且注意在LINUX系统下用：分割表示并列，而不是windo的； 3和4都是在用户目录下的，他们唯一的不同是: .bash_profile只能在登录的时候启动一次。在我的Ubuntu里面这个3文件似乎没有。

5)~/.bash_logout:当每次退出系统(退出bash shell)时,执行该文件. 另外,/etc/profile中设定的变量(全局)的可以作用于任何用户,而~/.bashrc等中设定的变量(局部)只能继承/etc/profile中的变量,他们是/"父子/"关系.

~/.bash_profile 是交互式、login 方式进入 bash 运行的 ~/.bashrc 是交互式 non-login 方式进入 bash 运行的 通常二者设置大致相同，所以通常前者会调用后者。

好的，总结一下他们的执行方式： 当你登录并且登录shell是bash时,bash首先执行/etc/profile文件中的命令(如果该文件存在),然后它顺序寻找~ /.bashprofile,~/.bashlogin或~/.profile文件,并执行找到的第一个可读文件中的命令.当登录bash退出时,它 将执行~/.bashlogout文件中的命令. 当启动一个交互的bash时,它将执行~/.bashrc文件中的命令(如果该文件存在并且可读).当非交互地启动以运行一个shell脚本时,bash将查找bashenv环境变量,确定执行文件的名称.