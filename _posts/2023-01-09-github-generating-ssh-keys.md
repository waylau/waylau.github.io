---
layout: post
title: GitHub生产SSH密钥
date: 2023-01-09 00:22
author: admin
comments: true
categories: [GitHub]
tags: [GitHub]
---

在初始使用GitHub的SSH方式检出工程的时候，是需要设置SSH密钥的。本文介绍生成SSH密钥的过程。

<!-- more -->

## 问题背景

在新电脑上，当试图检出GitHub工程的时候，会提示如下告警“Permission denied (publickey)”。

```
D:\workspace\github>git clone git@github.com:waylau/waylau.github.io.git
Cloning into 'waylau.github.io'...
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

此时就需要去生成SSH密钥



## 设置Git的账号和邮箱

因为是新电脑，所以在安装完Git之后，需要设置账号和邮箱：

```
D:\workspace\github>git config --global --list
fatal: unable to read config file 'C:/Users/wayla/.gitconfig': No such file or directory

D:\workspace\github>git config --global user.name waylau

D:\workspace\github>git config --global user.email waylau521@gmail.com


D:\workspace\github>git config --global --list
user.name=waylau
user.email=waylau521@gmail.com
```


其中`--list`用于查看Git配置，`user.name`和`user.email`用于设置账号和邮箱。



## 生成SSH密钥



生成SSH密钥示例如下：

```
D:\workspace\github>ssh-keygen -t rsa -C waylau521@gmail.com
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\wayla/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\wayla/.ssh/id_rsa
Your public key has been saved in C:\Users\wayla/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:I+AU5AdjoMDyWQ6paW8rTK7LLLXXXO9LAHBe5ZkXdhmHYU waylau521@gmail.com
The key's randomart image is:
+---[RSA 3072]----+
|+ .+B +...++     |
|++o=.* + E       |
|o=+==-+          |
|+oo*.o           |
|..* . . S        |
| o.*   . .       |
|+ +..            |
|.=.o .           |
|+.o.o            |
+----[SHA256]-----+
```



此时就会生成私钥和公钥文件在`C:\Users\wayla/.ssh/id_rsa`和`C:\Users\wayla/.ssh/id_rsa.pub`路径。

## 添加公钥文件到GitHub账号


步骤如下：

1. 进入Settings.
2. 在 "Access"点击“SSH and GPG keys”
3. 点击“New SSH key”I
4. 添加SSH公钥，内容复制id_rsa.pub中的内容



## 验证

成功执行如下命令，则验证通过：

```
D:\workspace\github>git clone git@github.com:waylau/waylau.github.io.git
Cloning into 'waylau.github.io'...
remote: Enumerating objects: 3140, done.
remote: Counting objects: 100% (571/571), done.
remote: Compressing objects: 100% (263/263), done.
remote: Total 3140 (delta 298), reused 561 (delta 288), pack-reused 2569Receiving objects:  99% (3123/3140), 37.18 MiB |Receiving objects: 100% (3140/3140), 37.39 MiB | 3.05 MiB/s, done.
Resolving deltas:  28% (463/1651)
Resolving deltas: 100% (1651/1651), done.
```


## 参考引用

* https://docs.github.com/en/authentication/connecting-to-github-with-ssh
* GitHub 帮助文档<https://github.com/waylau/github-help>
* 原文同步至<https://waylau.com/github-generating-ssh-keys/>