---
layout: post
title: Docker 在 CentOS 下的安装、使用
date: 2016-02-22 02:41
author: admin
comments: true
categories: [Docker,CentOS]
tags: [Docker,CentOS,安装,容器,VirtualBox]
---
本文介绍了 Docker 在 CentOS 环境下的详细安装过程，并尝试运行一个最简单的 image 来感受下 Docker 的风采。

<!-- more -->

## 什么是 Docker

![](https://docs.docker.com/dist/assets/images/logo.png)

Docker 是为开发人员和系统管理员用于构建、发布、并运行分布式应用程序的开放式平台。该平台由 Docker 引擎（一个便携、轻巧的运行时和打包工具） 和 Docker Hub （一个共享应用程序和自动化工作流的云服务）等组成。Docker 可以使应用程序从组件迅速组装并消除了开发、质量保证和生产环境之间的摩擦问题。这样一来，IT部门可以更快地发布，而这些应用程序不管是运行在笔记本电脑、数据中心的虚拟机，还是任何的云，其运行过程和结果都是一致的。

更多有关 Docker 的介绍，可以参阅《[简述 Docker](http://waylau.com/ahout-docker/)》 一文。

## 前置条件

* 64-bit 系统
* kernel 3.10+
* CentOS 7+。本例使用的是`CentOS-7-x86_64-Minimal-1511.iso`（如果你没有 CentOS 系统，可以尝试在虚拟机里面装个 CentOS 系统，可以参阅《[Oracle VM VirtualBox 安装和使用](http://waylau.com/about-oracle-vm-virtualbox/)》《[CentOS 7 安装、配置](http://waylau.com/centos-7-installation-and-configuration/)》）

使用 `uname -r` 检查 kernel 版本

    $ uname -r
    3.10.0-327.el7.x86_64

建议你使用最新的系统，以为一直的 bug 都会在新的 kernel 发布中修复。

## 安装

### yum 方式安装

1.使用 sudo 或 root 权限的用户登入系统。

2.确保你的 yum  是最新的

    sudo yum update
    
3.添加 yum 仓库

    $ sudo tee /etc/yum.repos.d/docker.repo <<-'EOF'
    [dockerrepo]
    name=Docker Repository
    baseurl=https://yum.dockerproject.org/repo/main/centos/$releasever/
    enabled=1
    gpgcheck=1
    gpgkey=https://yum.dockerproject.org/gpg
    EOF

注意，只能用`[Ctrl]d`结束，而不能用`[Ctrl]c`了

4.安装 Docker 包

    $ sudo yum install docker-engine


出现下面错误

    Loaded plugins: fastestmirror
    
就是 fastestmirror 插件错误，可以禁用该插件也可以暂时不理会，这并不影响 docker-engine 的安装。

禁用该 fastestmirror 插件

    vi /etc/yum/pluginconf.d/fastestmirror.conf
    enabled=1改成0


## 启动 docker 守护进程

    $ sudo  systemctl start docker.service

## 配置让 docker 服务随系统自动启动

    $ sudo chkconfig docker on

## 验证 docker 是否安装成功

    $ sudo systemctl enable docker.service
    
如果出错：

    [waylau@waylau ~]$ sudo docker run hello-world
    [sudo] password for waylau:
    Sorry, try again.
    [sudo] password for waylau:
    Unable to find image 'hello-world:latest' locally
    docker: Error response from daemon: Get https://registry-1.docker.io/v2/library/hello-world/manifests/latest: Get https://auth.docker.io/token?scope=repository%3Alibrary%2Fhello-world%3Apull&service=registry.docker.io: dial tcp: lookup auth.docker.io on 8.8.8.8:53: no such host.
    
添加当前用户`waylau`到 docker 用户组

    $ sudo usermod -aG docker waylau

并重复多几次（最好能翻墙，毕竟服务器在国外）最后成功的界面如下：

    [waylau@waylau ~]$ sudo docker run hello-world
    Unable to find image 'hello-world:latest' locally
    Pulling repository docker.io/library/hello-world
    975b84d108f1: Pull complete
    3f12c794407e: Pull complete
    Status: Downloaded newer image for hello-world:latest
    docker.io/library/hello-world: this image was pulled from a legacy registry.  Important: This registry version will not be supported in future versions of docker.
    
    Hello from Docker.
    This message shows that your installation appears to be working correctly.
    
    To generate this message, Docker took the following steps:
     1. The Docker client contacted the Docker daemon.
     2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
     3. The Docker daemon created a new container from that image which runs the
        executable that produces the output you are currently reading.
     4. The Docker daemon streamed that output to the Docker client, which sent it
        to your terminal.
    
    To try something more ambitious, you can run an Ubuntu container with:
     $ docker run -it ubuntu bash
    
    Share images, automate workflows, and more with a free Docker Hub account:
     https://hub.docker.com
    
    For more examples and ideas, visit:
     https://docs.docker.com/userguide/

整个过程是这样的：

1. Docker 客户端连接到 Docker 守护进程；
2. Docker 守护进程从 Docker Hub 中拉取名为 "hello-world" 的 image（镜像）；
3. Docker 守护程序从该 image 中创建新的容器，该容器执行输出动作，输出的内容就是上面所看到的；
4. Docker 守护程序将输出流到 Docker 客户端并发送你的终端显示。

## 查看 docker image

可以看到 pull 回来的这个 image 的一个情况，大小不到 1k。

    [waylau@waylau ~]$ docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    hello-world         latest              690ed74de00f        4 months ago        960 B

## 参考

* <https://docs.docker.com/engine/installation/linux/centos/>
* 《[Oracle VM VirtualBox 安装和使用](http://waylau.com/about-oracle-vm-virtualbox/)》
* 《[CentOS 7 安装、配置](http://waylau.com/centos-7-installation-and-configuration/)》