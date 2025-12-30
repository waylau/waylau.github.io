---
layout: post
title: Amazon EC2云服务器搭建OpenVPN
date: 2025-12-30 00:22
author: admin
comments: true
categories: [OpenVPN]
tags: [OpenVPN, AWS]
---




本文演示如何使用Amazon EC2云服务器来搭建OpenVPN。

<!-- more -->


## 登录AWS管理控制台创建EC2实例

登录AWS管理控制台<http://aws.amazon.com/console>


## 创建EC2实例

进入EC2控制台，操作系统可以选自己熟悉的，比如Ubuntu。实例名字自己定义，比如填“openvpn”。点击“启动实例”。


创建密钥，比如“openvpn-key.pem”，然后下载。

## 配置EC2实例

在EC2实例中，选择安全组，入站规则加一条：使用UDP 1194端口。这个是OpenVPN的端口，UDP端口号可以自己定义。

## 使用脚本配置OpenVPN服务器


切换到root模式。

```bash
sudo root
```

没有root用户，可以启用下

```bash
sudo passwd root 
```


下载执行脚本

```bash
curl -O https://raw.githubusercontent.com/angristan/openvpn-install/master/openvpn-install.sh
chmod +x openvpn-install.sh
```

运行脚本


```
./openvpn-install.sh interactive
```

交互过程中，可以都选择默认的选项。最后一步会要求输入一个OpenVPN客户端的名称，可以自定义比如`waylau`。脚本执行完成后，会生成一个配置文件，在`/root/`目录下会有一个 `waylau.opvn` 文件。复制该文件内容或者下载该文件到你的本地，然后使用OpenVPN客户端配置导入这个.opvn 文件建立连接即可。