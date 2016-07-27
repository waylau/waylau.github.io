---
layout: post
title: CentOS 7 安装、配置
date: 2015-05-12 02:41
author: admin
comments: true
categories: [CentOS]
tags: [CentOS]
---

## 下载

地址：<http://www.centos.org/download/>

本例为 Minimal ISO 版本：<http://isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-1503-01.iso>

CentOS 与 RHEL 是同源，所以，在 CentOS 文档不足时，可以参考 RHEL 的文档。<https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/index.html>

<!-- more -->

## 制作U盘启动工具

本例子环境为：Windows

可以使用 Fedora LiveUSB Creator(<https://fedorahosted.org/liveusb-creator/>) 或者 UltraISO 等工具来把系统写入 U盘，而后通过U盘启动来安装系统。

## 安装系统

从U盘启动，安装系统。安装中，会提示选择

* 默认选择 “Test this media & Install CentOS 7”

![](http://99btgc01.info/uploads/2015/06/001.png)


* 选择语言，设置时区

  本例为中文

![](http://99btgc01.info/uploads/2015/06/002.png)

* 设置系统安装位置

  选择默认的“自动分配分区”

![](http://99btgc01.info/uploads/2015/05/003%286%29.jpg)

* 设置主机名称和IP
![](http://99btgc01.info/uploads/2015/06/003.png)

![](http://99btgc01.info/uploads/2015/06/004.png)

* 设置 root 密码、创建新用户

![](http://99btgc01.info/uploads/2015/05/004%283%29.jpg)


## 配置静态IP

### 查看当前网络情况

执行 `ip addr` 
    
    [root@localhost ~]#  ip addr
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
           valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
           valid_lft forever preferred_lft forever
    2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether 08:00:27:78:33:ff brd ff:ff:ff:ff:ff:ff
        inet 192.168.0.106/24 brd 192.168.0.255 scope global dynamic enp0s3
           valid_lft 82545sec preferred_lft 82545sec
        inet6 fe80::a00:27ff:fe78:33ff/64 scope link
           valid_lft forever preferred_lft forever
    
### 查看以太网设备名称

    [root@localhost ~]# nmcli d
    设备   类型      状态    CONNECTION
    ens33  ethernet  连接的  ens33
    lo     loopback  未管理  --

其中，类型为 ethernet 的那个 “ens33”设备，就是以太网。因为该名称不是固定的，比如在虚拟机里面安装时，名称可能是“enp0s3”。

### 查看所有网络配置文件

执行 ls /etc/sysconfig/network-scripts/，查看所有网络配置文件


    [root@localhost ~]# ls  /etc/sysconfig/network-scripts/
    ifcfg-ens33  ifdown-ppp       ifup-ib      ifup-Team
    ifcfg-lo     ifdown-routes    ifup-ippp    ifup-TeamPort
    ifdown       ifdown-sit       ifup-ipv6    ifup-tunnel
    ifdown-bnep  ifdown-Team      ifup-isdn    ifup-wireless
    ifdown-eth   ifdown-TeamPort  ifup-plip    init.ipv6-global
    ifdown-ib    ifdown-tunnel    ifup-plusb   network-functions
    ifdown-ippp  ifup             ifup-post    network-functions-ipv6
    ifdown-ipv6  ifup-aliases     ifup-ppp
    ifdown-isdn  ifup-bnep        ifup-routes
    ifdown-post  ifup-eth         ifup-sit

其中，“ifcfg-ens33” 就是我们要修改的以太网配置文件（在虚拟机里面，文件名可能是 “ifcfg-enp0s3”，即该文件名与你的以太网设备名字相关）

### 修改网络配置文件

用 root 账户登录系统 修改以太网设备配置文件

    vi /etc/sysconfig/network-scripts/ifcfg-ens33
  
原先的配置是 ONBOOT 是默认不启动网卡的,改为 yes


![](http://99btgc01.info/uploads/2015/06/005.png)

设置完成执行 systemctl restart network 重启网络，而后 ping 下这个 IP 做下测试

## 安装时间服务器

安装

	yum install chrony

查看状态

	systemctl status chronyd

启动\关闭时间服务器

	systemctl start chronyd

	systemctl stop chronyd

设置与系统自启动\关闭

	systemctl enable chronyd

	systemctl disable chronyd

## 校时

执行 timedatectl 查看当前时间情况：

    [root@bogon ~]# timedatectl
          Local time: 四 2015-05-14 19:07:03 CST
      Universal time: 四 2015-05-14 11:07:03 UTC
            RTC time: 四 2015-05-14 11:07:03
            Timezone: Asia/Shanghai (CST, +0800)
         NTP enabled: no
    NTP synchronized: no
     RTC in local TZ: no
          DST active: n/a

设置 NTP 服务器同步

    timedatectl set-ntp yes 

再次查看时间状态

    [root@bogon ~]# timedatectl status
          Local time: 四 2015-05-14 11:15:39 CST
      Universal time: 四 2015-05-14 03:15:39 UTC
            RTC time: 四 2015-05-14 11:14:47
            Timezone: Asia/Shanghai (CST, +0800)
         NTP enabled: yes
    NTP synchronized: yes
     RTC in local TZ: no
          DST active: n/a
      
查看当前时间，执行

    date

显示

    [root@bogon ~]# date
    2015年 05月 14日 星期四 11:15:54 CST

##  添加用户、授权

添加用户

    useradd dev #创建用户  
    passwd dev #修改密码 
    
   
新建用户添加 sudo 权限

    chmod -v u+w /etc/sudoers  
    
`/etc/sudoers` 的权限模式保留为0640 (rw-r-----)  

在 sudoers 文件添加新用户信息到 ## Allow root to run any commands anywher 下，
 
    root    ALL=(ALL)       ALL  
    dev     ALL=(ALL)       ALL #新增用户信息  
    
取消 sudoers 文件可写权限

    chmod -v u-w /etc/sudoers  