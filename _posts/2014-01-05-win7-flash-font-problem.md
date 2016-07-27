---
layout: post
title: win7下Flash字体显示异常，解决
date: 2014-01-05 02:55
author: admin
comments: true
categories: [Flash]
---
刚换了win7系统 ，原有Flex项目中的字体显示出现不一致的情况，如下图
<img alt="图片" src="http://e.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=f8ebda6031fa828bd5239de6cd243009/b03533fa828ba61eec06964a4334970a314e59ee.jpg?referer=5f00c63bdfc451daafe138dba7a4&amp;x=.jpg" data-img-idx="0" data-src="http://e.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=f8ebda6031fa828bd5239de6cd243009/b03533fa828ba61eec06964a4334970a314e59ee.jpg?referer=5f00c63bdfc451daafe138dba7a4&amp;x=.jpg" />

且，几部电脑安装该系统盘均出现上述情况，而用另外系统碟装的系统却正常。
由于该Flash项目调用的是系统中的字库，可初步判断是字库的问题 。

解决：更换系统字库
在C:\Windows\Fonts 目录下 存放的就是系统所有的字体，
更换也挺简单，只需要复制其他系统的全部字体过来，覆盖该系统的字体即可
<img alt="图片" src="http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=a9d3d7645e6034a82de2b884fb283869/bd315c6034a85edfbd47a0464b540923dc54758f.jpg?referer=afcb9389d300baa1e33b738bb385&amp;x=.jpg" data-img-idx="1" data-src="http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=a9d3d7645e6034a82de2b884fb283869/bd315c6034a85edfbd47a0464b540923dc54758f.jpg?referer=afcb9389d300baa1e33b738bb385&amp;x=.jpg" />
