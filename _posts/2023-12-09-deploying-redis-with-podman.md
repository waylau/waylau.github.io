---
layout: post
title: 使用Podman对Redis进行容器化部署
date: 2023-12-09 00:22
author: admin
comments: true
categories: [Podman,Redis]
tags: [Podman,Redis]
---


本文演示了如何在Linux（以Ubuntu为例）安装Podman，并通过Podman部署Redis。

<!-- more -->

## Podman的安装、使用

Podman的安装和基本使用，参见前文[《使用Podman对MySQL进行容器化部署》](https://waylau.com/deploying-mysql-server-with-podman/)。


## 拉取image

```
$ podman pull docker.io/library/redis:7.2.3
```




## 准备Redis配置文件

准备好Redis的配置文件`container-engine-redis.conf`到`/etc/`目录下


```
sudo mv container-engine-redis.conf /etc/
```



## 创建Redis挂载路径


创建目录并授权：

```
sudo mkdir -p -m 777 /data/redis/container-engine-redis/
```


给数据目录授权：


```
sudo chown 165562:165562 /data/redis/container-engine-redis/
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
$ podman run --cpus=4 --memory=8g -e TZ="Asia/Shanghai" --restart=always   -p 6380:6379 --name=container-engine-redis  --privileged=true -v /etc/container-engine-redis.conf:/etc/redis/redis.conf -v /data/redis/container-engine-redis:/data    -d    docker.io/library/redis:7.2.3 redis-server    /etc/redis/redis.conf --requirepass yourpasswd
b547b1ca57b86be27d8f2cbb84a0a6c12d5976dfe46d9972a42552039a8c3541
```


## 开放端口

开放 6391 端口

```
sudo ufw allow 6391
```

## 进入容器执行命令

```
podman exec -it container-engine-redis redis-cli -p 6379
```

登录Redis执行命令：

```
$ podman exec -it container-engine-redis redis-cli -p 6379
127.0.0.1:6380> config get requirepass
(error) NOAUTH Authentication required.
127.0.0.1:6380> auth yourpasswd
OK
127.0.0.1:6380> config get requirepass
1) "requirepass"
2) "yourpasswd"
127.0.0.1:6380> 
```