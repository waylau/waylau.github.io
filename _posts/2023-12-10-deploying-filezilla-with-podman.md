---
layout: post
title: 使用Podman对FileZilla进行容器化部署
date: 2023-12-10 00:22
author: admin
comments: true
categories: [Podman,FileZilla]
tags: [Podman,FileZilla]
---


本文演示了如何在Linux（以Ubuntu为例）安装Podman，并通过Podman部署FileZilla。

<!-- more -->

## Podman的安装、使用

Podman的安装和基本使用，参见前文[《使用Podman对MySQL进行容器化部署》](https://waylau.com/deploying-mysql-server-with-podman/)。


## 拉取image

```
$ podman pull docker.io/jlesage/filezilla:latest
```






## 创建FileZilla挂载路径


创建目录并授权：

```
sudo mkdir -p -m 777 /data/filezilla/container-filezilla-ftp/ /data/ftp/
```


给数据目录授权：


```
sudo chown 165562:165562 /data/filezilla/container-filezilla-ftp/ /data/ftp/
```


## 给CPU、内存的权限（可选）

给CPU、内存的权限：


创建delegate.conf 内容如下：


```
[Service]
Delegate=memory pids cpu cpuset
```

```
sudo mkdir -p /etc/systemd/system/user@.service.d/

sudo mv delegate.conf /etc/systemd/system/user@.service.d/
```

重启系统，或者执行下面命令生效：



```
sudo systemctl daemon-reload
```


## 运行容器

为运行容器：

```
$ podman run --cpus=2 --memory=4g -e TZ="Asia/Shanghai"  -e   ENABLE_CJK_FONT=1  --restart=always   -p 5801:5800 --name=container-filezilla-ftp   --privileged=true -v /data/filezilla/container-filezilla-ftp:/config:rw     -v /data/ftp:/storage:rw    docker.io/jlesage/filezilla:latest
```

* `/data/filezilla`: 应用的configuration, states, log 等文件
* `/data/ftp`: FTP文件目录
* ENABLE_CJK_FONT: 可选项，设置为 1 时，将安装开源字体 WenQuanYi Zen Hei 。该字体包含各种中文/日文/韩文字符


## 开放端口

开放 5801 端口

```
sudo ufw allow 5801
```

## 浏览器访问


访问：<http://192.168.1.70:5801>
