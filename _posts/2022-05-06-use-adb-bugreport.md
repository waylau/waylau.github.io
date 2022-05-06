---
layout: post
title: 使用adb bugreport获取Android设备上的错误报告
date: 2022-05-06 00:22
author: admin
comments: true
categories: [ADB]
tags: [ADB]
---


有时候，应用启动报错，但无法从应用日志中获取到有效的信息，此时，就需要从Android设备上操作系统层级来获取日志。adb bugreport可以获取到Android设备上的错误报告。

<!-- more -->


首先，确保设备已启用开发者选项。

其次，用你的PC连接了上这台设备。



执行以下命令看下是否脸上了设备：

```
adb devices
List of devices attached
R38MB04CDRH     unauthorized
```

如果上述设备是“unauthorized”，则在开发者模式里面点击“撤销USB调试授权”。再重新开启USB调试。
重复上述命令，如果是下面的状态，设备正常连接了，可以调试了：

```
adb devices
List of devices attached
R38MB04CDRH     device
```

最后执行下面的命令：

```
adb bugreport D:\
```

其中，上述“D:\”是你在PC上指定错误报告的路径。
