---
layout: post
title: 用Heroku、snova-c4搭梯子上墙
date: 2014-04-03 17:32
author: admin
comments: true
categories: [Heroku,Web]
tags: [Heroku, snova-c4, Web]
---
1.snova-c4（翻墙软件）下载

https://code.google.com/p/snova/downloads/list

服务端<https://snova.googlecode.com/files/snova-c4-java-server-0.22.0.war>

客户端<https://snova.googlecode.com/files/gsnova_0.22.1_windows_386.zip>

2.在 heroku.com 部署 snova-c4-java-server-0.22.0.war

	heroku login

	heroku plugins:install https://github.com/heroku/heroku-deploy --只需执行一次，以后不用执行

	heroku apps:create --此步创建一个app，名字随机，记住此步的appname（"xx.herokuapp.com"）。更新不用执行此步

	heroku deploy:war --war <path_to_war_file> --app <app_name>

留意执行“heroku apps:create”时的输出，一般会显示创建的域名，为 "xx.herokuapp.com", 记下该域名，为配置Client准备

输入域名，见下面页面，则说明部署成功

<img class="alignnone" alt="" src="http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=14c620458fb1cb133a693c16ed6f2773/5fdf8db1cb134954294da3ce544e9258d0094acc.jpg?referer=9acfb38b708b47109738c9fcb6c2&amp;x=.jpg" width="550" height="224" />

3.解压 gsnova_0.22.1_windows_386.zip

修改gsnova.conf

将GAE的Enable设置为0

C4的Enable设置为1，WorkerNode[0]后面写上你的Heroku二级域名"xx.herokuapp.com"

SPAC的Enable设置为0，确定保存

双击打开gsnova.exe可执行文件。

4.浏览器设置代理

google 就装switchysharp

代理设置端口时要与你在配置文件中设置的端口是一致的。

默认是127.0.0.1 端口48102

<img class="alignnone" alt="" src="http://f.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=e4cac96a80025aafd7327ececbd6da59/72f082025aafa40f7e224177a964034f79f019b7.jpg?referer=b20f9de59c2f070806121e30676e&amp;x=.jpg" width="550" height="296" />

翻墙成功

<img class="alignnone" alt="" src="http://f.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=9320d649ff1f4134e437057b1524e4f7/a9d3fd1f4134970a05705b8597cad1c8a7865d1c.jpg?referer=b4f54741b4fd5266fe3c08249bf2&amp;x=.jpg" width="550" height="315" />
