---
layout: post
title: Failed to start Docker Application Container Engine
date: 2024-09-02 00:22
author: admin
comments: true
categories: [Docker]
tags: [Docker,VirtualBox,Ubuntu]
---

今日VirtualBox异常退出，重启之后，Ubuntu系统下的Docker守护进程却无法启动。本文记录解决方案。


<!-- more -->



## 问题背景

VirtualBox异常退出，重启之后，Ubuntu系统下的Docker守护进程却无法启动，报错信息如下。

```
$ sudo service docker restart
Job for docker.service failed because the control process exited with error code.
See "systemctl status docker.service" and "journalctl -xeu docker.service" for details.

$ systemctl status docker.service
× docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: failed (Result: exit-code) since Thu 2024-07-04 16:03:35 CST; 4s ago
TriggeredBy: × docker.socket
       Docs: https://docs.docker.com
    Process: 1706 ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock (code=exited, status=1/FAILURE)
   Main PID: 1706 (code=exited, status=1/FAILURE)
        CPU: 94ms

Jul 04 16:03:35 ubuntu-app-uat systemd[1]: docker.service: Scheduled restart job, restart counter is at 3.
Jul 04 16:03:35 ubuntu-app-uat systemd[1]: Stopped Docker Application Container Engine.
Jul 04 16:03:35 ubuntu-app-uat systemd[1]: docker.service: Start request repeated too quickly.
Jul 04 16:03:35 ubuntu-app-uat systemd[1]: docker.service: Failed with result 'exit-code'.
Jul 04 16:03:35 ubuntu-app-uat systemd[1]: Failed to start Docker Application Container Engine.
```

删除配置之后，就能启动
```
sudo rm -f /etc/docker/daemon.json
sudo systemctl start docker
```

因此删除了配置，Docker就会使用默认的数据目录，因此就能正常启动。因此怀疑是 Docker 数据目录损坏，先移除掉：

```
sudo rm -rf /data/docker
```

再把daemon.json文件加回去，重启

```
sudo reboot
```

可用正常启动的。唯一的遗憾是之前所创建的容器都删除掉了，需要重建。