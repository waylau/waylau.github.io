---
layout: post
title: SVN 1.8.x 服务器安装
date: 2014-12-20 01:21
author: admin
comments: true
categories: [SVN]
tags: [SVN]
---

##一、	SVN 服务器下载、安装

###1.	关于 Subversion 1.8.11
Apache Subversion 1.8.11 发布说明

<http://mail-archives.apache.org/mod_mbox/subversion-dev/201412.mbox/%3C548F4EF1.9070900@apache.org%3E>

###2.	兼容性问题
Apache Subversion 1.8.x 兼容性说明

<http://subversion.apache.org/docs/release-notes/1.8.html>

<!-- more -->

旧客户端和服务器与 1.8 服务器和客户端互操作是透明的。然而,1.8 的一些新特性可能不可用,除非客户端和服务器都是最新版本。还有另外的情况下,当客户端是新的，服务器是旧的，新功能可以用但运行可能比较低效。

不需要转储和重新加载您的存储库。Subversion 1.8 服务器可以读取和写入由早期版本创建的存储库。升级现有的服务器，仅需安装最新的库和二进制文件到旧的上面。

Subversion 1.8保持与较早版本的 API/ABI 兼容性,仅添加新功能,从来没有删除旧的。一个程序写入任何以前 1.x 版本的 API都可以编译和运行使用 1.8 的库。然而,给 1.8 编写的程序不能编译或运行在旧的库上。

有可能在有限的情况下，旧的行为的API从以前的版本中已略作修改。在这些情况下，功能的边缘情况，被认为是错误，并因此改善或消除。请查阅[API勘误表](http://svn.apache.org/repos/asf/subversion/trunk/notes/api-errata/1.8/)上的这些API，获取这些变化可能有什么影响的更详细的信息。

###3.	安装包下载
Apache 本身不提供安装包下载，由第三方提供

<http://subversion.apache.org/packages.html#windows>

####3.1 VisualSVN 

下载说明
<https://www.visualsvn.com/server/>

带管理界面。服务器免费，客户端收费。包含 Apache Subversion 1.8.11

最新版本 3.2
配置要求 

System Requirements
Operating Systems
Windows Server 2008 or later
Windows Vista or later
Minimum hardware
1.4 GHz CPU

不采用，因为配置不够

####3.2 Win32Svn 

说明
<http://alagazam.net/>

Should work (but not tested) on all flavours of Windows
from Win2000 to Win8 including server variants.
(1.7.x does not work on NT4 due to APR using new functions).
512 MB RAM
50 MB hard drive space

只有svn的内核，没有界面，目前最新为 1.8.10。无需注册

####3.3 SlikSVN 

说明<https://sliksvn.com>

只有客户端下载。sever 是云服务器

####3.4 WANdisco 

下载说明<http://www.wandisco.com/subversion/download>


目前 svn 最新为 1.8.10 ，需要注册。客户端 smartSvn收费

####3.5 CollabNet 

下载说明<http://www.collabnet.cn/downloads/subversion>

功能选择比较多，是 svn 的创始者。没有界面。目前 svn 最新为 1.8.10。需要注册


由于现有的服务器操作系统是 Windows Server 2003,最后服务器选了 win32svn   1.8.10

##二、	SVN客户端

可使用原有的旧版本的客户端。如果要升级，建议使用 tortoisesvn 1.8.10，开源， <http://tortoisesvn.net/downloads.html>。对于 Windows XP SP3 系统用户,需要额外安装[Windows Installer 4.5](http://www.microsoft.com/en-us/download/details.aspx?id=8483)

##三、	SVN 服务器配置

###1.安装目录

C:\Program Files\Subversion

###2.工作目录

E:\svn

###3.建立版本库（Repository） 

执行 `svnadmin create E:\svn\repository`。注：首次安装完需要重启。这里svn文件夹要先建立好。 

###4.配置用户和权限 

####4.1 svnserve.conf文件

打开 E:\svn\repository, 你会发现已经多了一些目录和文件, 打开conf子目录, 打开svnserve.conf文件, 这里行前凡是有#的都等于是被注释忽略了, 你可以把#去掉让那一行生效, 或者自己新添加行. 里面的英文注释已经详细说明了各种设置的含义, 最后你设置 [general]小节中行前没有#号的内容为: 

	[general]
	anon-access = read
	auth-access = write 
	password-db = passwd 
	authz-db = authz

####4.2 passwd 文件 

同样, 设置[users]小节中行前没有#号的内容, 例如: 

	[users]
	lww = 123
	lww2 = 123

含义是: 

用户lww、ww2的密码为123 

####4.3 配置 authz文件

	[groups]
	dept_test = lww2    

	[/]				 
	lww = rw

	[/ui-compressor]   
	@ dept_test = r

含义是：用户 lww2 在 `dept_test` 分组下面；
根目录下所有文件都有读写权限；对 `dept_test` 组下所有成员有 `ui-compressor` 读的权限

##四、 使用案例

###1.启动svn服务
执行 
	svnserve -d -r E:\svn\repository

###2.上传项目

右键->TortoiseSVN->Import
 
<img src="http://99btgc01.info/uploads/2014/12/001%284%29.jpg" alt="001(4).jpg" title="001(4).jpg" />

输入svn 所在 URL
 
<img src="http://99btgc01.info/uploads/2014/12/002%281%29.jpg" alt="002(1).jpg" title="002(1).jpg" />

输入用户账号、密码
 
<img src="http://99btgc01.info/uploads/2014/12/003%282%29.jpg" alt="003(2).jpg" title="003(2).jpg" />

最好点击 save authentication,来保存账号密码
 
<img src="http://99btgc01.info/uploads/2014/12/004%281%29.jpg" alt="004(1).jpg" title="004(1).jpg" />

右键-> Add folder->选择要上传的项目

<img src="http://99btgc01.info/uploads/2014/12/005.jpg" alt="005.jpg" title="005.jpg" />

<img src="http://99btgc01.info/uploads/2014/12/006%281%29.jpg" alt="006(1).jpg" title="006(1).jpg" />
 
提交时，输入日志信息
 
<img src="http://99btgc01.info/uploads/2014/12/007.jpg" alt="007.jpg" title="007.jpg" />

完成
 
<img src="http://99btgc01.info/uploads/2014/12/008%281%29.jpg" alt="008(1).jpg" title="008(1).jpg" />

###3.检出项目

右键->SVN Checkout

<img src="http://99btgc01.info/uploads/2014/12/009.jpg" alt="009.jpg" title="009.jpg" />
 
输入存储库的 URL
 
<img src="http://99btgc01.info/uploads/2014/12/010.jpg" alt="010.jpg" title="010.jpg" />

输入账号密码 
 
<img src="http://99btgc01.info/uploads/2014/12/012.jpg" alt="012.jpg" title="012.jpg" />

###4.更新、提交代码

修改了代码
 
<img src="http://99btgc01.info/uploads/2014/12/013.jpg" alt="013.jpg" title="013.jpg" />

右键-> SVN Update
 
<img src="http://99btgc01.info/uploads/2014/12/014.jpg" alt="014.jpg" title="014.jpg" />
 
<img src="http://99btgc01.info/uploads/2014/12/015%281%29.jpg" alt="015(1).jpg" title="015(1).jpg" />

输入提交信息

<img src="http://99btgc01.info/uploads/2014/12/016%281%29.jpg" alt="016(1).jpg" title="016(1).jpg" />
 
###5.	授权

使用只有 r 权限的用户lww 2

<img src="http://99btgc01.info/uploads/2014/12/017.jpg" alt="017.jpg" title="017.jpg" />
  
<img src="http://99btgc01.info/uploads/2014/12/018.jpg" alt="018.jpg" title="018.jpg" />

提交时，由于lww2 无 w权限，则会无法通过验证

<img src="http://99btgc01.info/uploads/2014/12/019.jpg" alt="019.jpg" title="019.jpg" />
 
###6.	设置开机自启

命令行执行 ：

	sc create subversion_service binpath= "C:/Program Files/Subversion/bin/svnserve.exe --service - r E:/svn/repository" displayname= "Subversion Repository" depend= Tcpip start= auto

根据实际情况作相应的修改。 

解释：subversion_service为服务的名称。所有的系统服务可以在dos下通过命令手动启动和停止。 

启动：net start subversion_service  

停止：net stop subversion_service 

binpath 为安装 subversion 的路径，可执行文件的路径。 

C:/projectname/repository为版本库的路径。 

displayname= "Subversion Repository"为注册成的系统服务显示名称，注册成功后， 可以在系统服务中找到这个名称。 

depend= Tcpip  为依赖Tcpip协议。 

start= auto为注册成的系统服务启动类型为自动



<img src="http://99btgc01.info/uploads/2014/12/sc1.jpg" alt="sc1.jpg" title="sc1.jpg" />

注册成功后，可以看到该服务

<img src="http://99btgc01.info/uploads/2014/12/sc2.jpg" alt="sc2.jpg" title="sc2.jpg" />

 
##五、 问题排查

###1. 遇到不可读的路径；拒绝访问。

做“显示资源历史记录”操作时，提示 “svn：遇到不可读的路径；拒绝访问。”

![](http://99btgc01.info/uploads/2015/03/svn.png)

解决：在项目的conf/svnserve.conf 中, 设置 anon-access = none 即可. 然后重启 Subversion 服务.

