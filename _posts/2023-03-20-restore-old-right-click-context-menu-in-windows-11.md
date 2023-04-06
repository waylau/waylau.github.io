---
layout: post
title: 在Windows 11中恢复旧的右键单击上下文菜单
date: 2023-03-20 00:22
author: admin
comments: true
categories: [Windows]
tags: [Windows]
---

在Windows 11中恢复旧的右键单击上下文菜单。

<!-- more -->


执行以下命令：

```
reg.exe add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve
```

重启电脑或者任务管理器后生效。



在Windows 11中恢复新的右键单击上下文菜单，则执行以下命令：

```
reg.exe delete "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}" /f
```