---
layout: post
title: 使用Podman对Nexus进行容器化部署
date: 2023-12-11 00:22
author: admin
comments: true
categories: [Podman,Nexus]
tags: [Podman,Nexus]
---


本文演示了如何在Linux（以Ubuntu为例）安装Podman，并通过Podman部署Nexus。

<!-- more -->

## Podman的安装、使用

Podman的安装和基本使用，参见前文[《使用Podman对MySQL进行容器化部署》](https://waylau.com/deploying-mysql-server-with-podman/)。

## 拉取image

```
$ podman pull docker.io/sonatype/nexus3:3.63.0

```





## 创建挂载路径


创建目录并授权：

```
sudo mkdir -p -m 777 /data/nexus/container-nexus/
```


给数据目录授权：


```
sudo chown 165562:165562 /data/nexus/container-nexus/
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
$ podman run --cpus=4 --memory=8g -e TZ="Asia/Shanghai" --restart=always   -p 8081:8081 --name=container-nexus  --privileged=true -v /data/nexus/container-nexus:/nexus-data    -d    docker.io/sonatype/nexus3:3.63.0
```


## 开放端口

开放 8081 端口

```
sudo ufw allow 8081

```

## 进入容器执行命令

查询admin账号密码：

```
$ podman exec -it 903127025d03 /bin/bash

bash-4.4$ cat /nexus-data/admin.password
84bc25d6-0f3e-420d-b306-508268bb79dd
```

上述`84bc25d6-0f3e-420d-b306-508268bb79dd`即为初始化密码


## 重置密码

访问<http://192.168.1.70:8081/>，用admin账号和上述`84bc25d6-0f3e-420d-b306-508268bb79dd`初始化密码登录系统，并重置密码。


## 配置


### 搭建Maven私服（代理仓库）

选“maven2(proxy)”。
* Name：可自定义
* Remote storage：`http://maven.aliyun.com/nexus/content/groups/public/`


### 搭建Maven私服（本地仓库）

选“maven2(hosted)”。
* Name：可自定义




## 参考

* <https://github.com/sonatype/docker-nexus3>