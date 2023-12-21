---
layout: post
title: 安装Tailscale
date: 2023-12-20 00:22
author: admin
comments: true
categories: [Tailscale,Ubuntu,Podman]
tags: [Tailscale,Ubuntu,Podman]
---


本文演示了如何在Linux（以Ubuntu为例）和Podman上安装Tailscale。

<!-- more -->


## 在Linux（以Ubuntu为例）上安装 Tailscale 

下载安装包：

```
curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/jammy.noarmor.gpg | sudo tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null
curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/jammy.tailscale-keyring.list | sudo tee /etc/apt/sources.list.d/tailscale.list
```


安装 Tailscale：


```
sudo apt-get update
sudo apt-get install tailscale
```



将机器连接到Tailscale网络并在浏览器中进行身份验证：

```
$ sudo tailscale up

To authenticate, visit:

        https://login.tailscale.com/a/d473a924caa

Success.
```

“Success”输出即代表已连接！可以通过运行以下程序找到Tailscale IPv4地址：


```
$ tailscale ip -4

100.78.208.12
```

## 在Podman上安装 Tailscale 

### 拉取image

```
$ podman pull ghcr.io/tailscale/tailscale:v1.56.0
```





### 创建挂载路径


创建目录并授权：

```
sudo mkdir -p -m 777 /data/tailscale/container-tailscale/
```


给数据目录授权：


```
sudo chown 165562:165562 /data/tailscale/container-tailscale/
```


### 生成密钥


`Settings -> Keys`，点击“Generate auth key…”生成密钥。




### 运行容器

为运行容器：


```
$ podman run -d --cpus=1 --memory=2g -e TZ="Asia/Shanghai" --restart=always  --name=container-tailscale  --privileged=true  --network=host -v /var/lib:/var/lib -v /data/tailscale/container-tailscale:/dev/net/tun  --env TS_AUTHKEY=tskey-auth-k3DE2H2CNTRL-wru8ZJGSraiidpRNkyctZis99gx6EUxD ghcr.io/tailscale/tailscale:v1.56.0
```

其中：

* 网络设置设置为host
* TS_AUTHKEY的值为上一步生成的密钥

发送邀请链接给需要的人员即可<https://login.tailscale.com/uinv/i2a53da0170c6aaa>


## 参考

* <https://www.wundertech.net/how-to-set-up-tailscale-on-docker>
* <https://www.right.com.cn/forum/thread-8265168-1-1.html>