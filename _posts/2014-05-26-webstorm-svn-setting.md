---
layout: post
title: WebStorm配置svn共享、检出项目
date: 2014-05-26 07:08
author: admin
comments: true
categories: [Web]
tags: [WebStorm,svn]
---
 
##安装svn
1下载[SlikSVN](http://subversion.apache.org/packages.html#windows)

2安装

我的是在`D:\Program Files\SlikSvn`目录下

3.在WebStorm中配置

file->settings->Version Contorl->subversion->with conmand line client
设置成你的`D:\Program Files\SlikSvn\bin\svn.exe`

##svn共享项目
1.share Project
![share Project](http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=b6274fc838f33a879a6d001ff6676105/b7003af33a87e95056343f9412385343fbf2b423.jpg?referer=4c3040f099504fc2fb488435f4e1&x=.jpg)

2.select target
![select target](http://c.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=b07621fbca95d143de76e42643cbf33f/d833c895d143ad4b23c28b3880025aafa50f065e.jpg?referer=dca2731cb11c87018fa186d66235&x=.jpg)

3.验证
![验证](http://g.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=5ba329db71f082022992913a7bc08ad0/d01373f082025aaf52e7b333f9edab64024f1a75.jpg?referer=57637f1e257f9e2f29222938662c&x=.jpg)

4.确认
![确认](http://f.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=24915583d31373f0f13f6f9a94343ac6/ac4bd11373f08202af7d5cc749fbfbedaa641b5f.jpg?referer=72d3594385d6277fb00506086536&x=.jpg)

##svn检出项目
1.选择项目
![选择项目1](http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=6222c113b4fd5266a32b3c119b23e616/38dbb6fd5266d016eacd53d0952bd40734fa3576.jpg?referer=fdfe724abb014a90d829728d732d&x=.jpg)

![选择项目2](http://f.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=5627f21960d9f2d3241124ea99d7fb2e/0bd162d9f2d3572c3c0c51248813632763d0c358.jpg?referer=c62be284530fd9f9f90061592d36&x=.jpg)

2.检出
![检出1](http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=7eda4b169e82d158bf8259b4b03168e5/b2de9c82d158ccbfc35278c21bd8bc3eb0354158.jpg?referer=3d962e40d63f87948ae87c1eaf36&x=.jpg)

![检出2](http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=3b5e312217ce36d3a60483350ac84bba/c8ea15ce36d3d539d6ebc23b3887e950342ab070.jpg?referer=d8e5832456e736d10104b838f02e&x=.jpg)

##相关问题的解决
如果遇到如下错误
“Cannot load supported formats: Cannot run program 'svn': CreateProcess error=2, ”
![错误](http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=2e9d4b7fd2a20cf44290feda46323a0b/b3b7d0a20cf431adbcf08fc14936acaf2edd982c.jpg?referer=4d36d43de2fe9925921b5d60d8e2&x=.jpg)
请回到最上面，进行“安装svn”这步骤