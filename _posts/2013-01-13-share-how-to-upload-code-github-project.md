---
layout: post
title:  如何在 GitHub 上传代码、分享项目
date: 2013-01-13 00:31
author: admin
comments: true
categories: [GitHub]
tags: [Git,GitHub]
---

本文介绍了如何使用GitHub 上传代码、分享项目。

<!-- more -->

## 首先当然是要创建github账户，登录<https://github.com>有邮箱即可，略

## 而后需要在本地安装Git软件，地址：<http://git-scm.com/downloads>（Git可以理解为svn等类型的代码管理软件），略

## 新建一个 repository（仓库）

填写Repository name 、Description，选择Initialize this repository with a README
<img src="http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=bb59dda8fadcd100c99cf82442b0362d/9e3df8dcd100baa14d8f301e4510b912c9fc2ef7.jpg?referer=c2399ba6ca95d1438361d0138aad&x=.jpg"/>


此时一个repository创建完成 ，自动生成README.md和.gitgnore文件。
<img src="http://f.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=87a6b046552c11dfdad1bf26531c13ed/f3d3572c11dfa9ec6f6c21b260d0f703908fc1d1.jpg?referer=b4709b6cd52a60590b07d52a2fcf&x=.jpg"/>


## 获取项目

先拷贝仓库地址
<img src="http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=0866a951b54543a9f11bfac92e2cfbbf/eac4b74543a9822674835c828882b9014b90ebd1.jpg?referer=c08b484460d9f2d3790610df55cf&x=.jpg"/>

点击“Fork”
<img src="http://g.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=4d628b7f17ce36d3a60483350ac84bba/c8ea15ce36d3d539a0d778663887e950342ab0a8.jpg?referer=aed9397956e736d10104b838f066&x=.jpg"/>

在切换到工作区间，本例子为D:\workspaceGit

用git命令将项目拷贝到工作区间，地址为上面的仓库地址 `git clone https://github.com/waylau/go_for_java_programmers.git`
<img src="http://c.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=e6b11ef536a85edffe8cfe26796f781e/5d6034a85edf8db191930bc10b23dd54574e74d1.jpg?referer=a25c9395b8a1cd115ca14610b4cf&x=.jpg"/>
此时本地工作区间便已经存在该项目
<img src="http://b.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=34b296bf2a34349b70066e80f9d164f3/d52a2834349b033b4a2a8b7f17ce36d3d439bdf0.jpg?referer=6d46cf8503e939010f15b90efbae&x=.jpg"/>
打开README.md里面即为项目描述

## 上传更新

添加项目代码或者文件到该项目下
<img src="http://b.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=a81fe513cb177f3e1434fc0840f44afa/a686c9177f3e6709b0ea325a39c79f3df9dc55a8.jpg?referer=add0776fe4cd7b89b07b0eb39366&x=.jpg"/>

用git添加要上传的代码文件, `add .` 表示添加所有 
>git add

添加评论或说明 
>git commit –m ‘新增项目’ 

用git命令连接远程的项目 
>git remote add upstream https://github.com/waylau/go_for_java_programmers.git

获取在本地没有修改过的文件中修改过的代码 
>git fetch upstream

提交代码。身份验证，填入GitHub的用户名和密码 
>git push origin master

<img src="http://f.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=a59f78bc2ff5e0feea1889046c5b4595/3b292df5e0fe9925e9b11ef536a85edf8cb171d1.jpg?referer=32898ba872cf3bc7b117f9dcbfcf&x=.jpg"/>
