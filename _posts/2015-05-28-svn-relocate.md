---
layout: post
title: SVN 主机IP变更的应对方式
date: 2015-05-28 01:41
author: admin
comments: true
categories: [SVN]
tags: [SVN]
---
 
SVN 主机IP 修改后，所有客户端需要相应的修改 SVN 的访问地址。

<!-- more -->

##	TortoiseSVN 配置（一般用于文档管理）


### 右键项目->TortoiseSVN->重新定位 (Relocate)

<img src="http://99btgc01.info/uploads/2015/05/001%281%29.png" alt="001(1).png" title="001(1).png" />

### 然后修改URL 到最新的IP地址

<img src="http://99btgc01.info/uploads/2015/05/002%281%29.png" alt="002(1).png" title="002(1).png" />

 改为

<img src="http://99btgc01.info/uploads/2015/05/003.png" alt="003.png" title="003.png" />
 
### 重新保存用户名、密码

<img src="http://99btgc01.info/uploads/2015/05/004.png" alt="004.png" title="004.png" />

## Eclipse 配置（一般用于源码管理）
### 在Eclipse中选择Windows-> Show View->others 就会出现【SVN资源库/SVN Repositories】，选中后，点击确认；

<img src="http://99btgc01.info/uploads/2015/05/005.png" alt="005.png" title="005.png" />
 
<img src="http://99btgc01.info/uploads/2015/05/006.png" alt="006.png" title="006.png" />

### 右键项目原有的地址，选择【重新定位/Relocate】
 
<img src="http://99btgc01.info/uploads/2015/05/007.png" alt="007.png" title="007.png" />

### 如果一个路径下有多个项目，还需选择具体的项目，点【下一步】
 
<img src="http://99btgc01.info/uploads/2015/05/008.png" alt="008.png" title="008.png" />

### 输入新的URL 地址，点【Finish】
 
<img src="http://99btgc01.info/uploads/2015/05/009.png" alt="009.png" title="009.png" />