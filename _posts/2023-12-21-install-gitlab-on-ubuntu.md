---
layout: post
title: 在Linux（以Ubuntu为例）上安装GitLab
date: 2023-12-21 00:22
author: admin
comments: true
categories: [GitLab,Ubuntu]
tags: [GitLab,Ubuntu]
---


本文演示了如何在Linux（以Ubuntu为例）上安装GitLab。

<!-- more -->

## Ubuntu Server 网络配置

安装前选择桥接网卡。

修改 `/etc/netplan/00-installer-config.yaml` 内容如下：
```
# This is the network config written by 'subiquity'
network:
  version: 2
  ethernets:
    enp0s3:
      addresses:
      - 192.168.1.71/24
      # gateway4: 192.168.168.2  # 2204以前的网关
      routes:
        - to: default # default 等效 0.0.0.0/0 等效 0/0   # could be 0/0 or 0.0.0.0/0 optionally
          via: 192.168.1.1  # 2204的新网关
      nameservers:
        addresses:
        - 192.168.1.1 # 本地
        - 8.8.8.8 # 谷歌
        - 8.8.4.4
        - 1.1.1.1
        - 223.6.6.6 # 阿里
        - 114.114.114.114
        - 180.76.76.76 # 百度
        - 119.29.29.29 # 腾讯
        - 101.226.4.6
        - 123.125.81.6
        search:
        - 192.168.1.1 # 本地
        - 8.8.8.8 # 谷歌
        - 8.8.4.4
        - 1.1.1.1
        - 223.6.6.6 # 阿里
        - 114.114.114.114
        - 180.76.76.76 # 百度
        - 119.29.29.29 # 腾讯
        - 101.226.4.6
        - 123.125.81.6
```

使用配置

```
sudo netplan apply
```


## 设置时区

```
sudo timedatectl set-timezone Asia/Shanghai
```

## 安装和配置必须的依赖项

```
sudo apt-get update
sudo apt-get install -y curl openssh-server ca-certificates tzdata perl
```


（可选）下一步，安装 Postfix 以发送电子邮件通知。如果您想使用其他解决方案发送电子邮件，请跳过此步骤并在安装极狐GitLab 后配置外部 SMTP 服务器。

```
sudo apt-get install -y postfix
```

在安装 Postfix 的过程中可能会出现一个配置界面，在该界面中选择“Internet Site”并按下回车。把“mail name”设置为您服务器的外部 DNS 域名并按下回车。如果还有其它配置界面出现，继续按下回车以接受默认配置

## 添加 GitLab Apt Repository

运行以下 curl 命令，添加 GitLab 存储库。它会自动检测你的 Ubuntu 版本，并设置相应的存储库。


```
curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
```
## 下载、安装极狐GitLab


运行下面的命令，把 EXTERNAL_URL 替换为你服务器的 hostname，将在你的 ubuntu 系统上自动安装和配置 gitlab-ce

```
sudo EXTERNAL_URL="http://192.168.1.71" apt install gitlab-ce=16.7.0-ce.0
```




看下以下内容输出，则证明安装完成：

```
NOTE: Because these credentials might be present in your log files in plain text, it is highly recommended to reset the password following https://docs.gitlab.com/ee/security/reset_user_password.html#reset-your-root-password.

gitlab Reconfigured!

       *.                  *.
      ***                 ***
     *****               *****
    .******             *******
    ********            ********
   ,,,,,,,,,***********,,,,,,,,,
  ,,,,,,,,,,,*********,,,,,,,,,,,
  .,,,,,,,,,,,*******,,,,,,,,,,,,
      ,,,,,,,,,*****,,,,,,,,,.
         ,,,,,,,****,,,,,,
            .,,,***,,,,
                ,*,.
  


     _______ __  __          __
    / ____(_) /_/ /   ____ _/ /_
   / / __/ / __/ /   / __ `/ __ \
  / /_/ / / /_/ /___/ /_/ / /_/ /
  \____/_/\__/_____/\__,_/_.___/
  

Thank you for installing GitLab!
GitLab should be available at http://192.168.1.71

For a comprehensive list of configuration options please see the Omnibus GitLab readme
https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/README.md

Help us improve the installation experience, let us know how we did with a 1 minute survey:
https://gitlab.fra1.qualtrics.com/jfe/form/SV_6kVqZANThUQ1bZb?installation=omnibus&release=16-6

Scanning processes...                                                                                                                                               
Scanning candidates...                                                                                                                                              
Scanning linux images...                                                                                                                                            

Running kernel seems to be up-to-date.

Restarting services...
Service restarts being deferred:
 /etc/needrestart/restart.d/dbus.service
 systemctl restart networkd-dispatcher.service
 systemctl restart systemd-logind.service
 systemctl restart unattended-upgrades.service
 systemctl restart user@1000.service

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
```


## 访问极狐GitLab 实例并登录


随机生成一个密码并存储在 `/etc/gitlab/initial_root_password` 文件中。出于安全原因，24 小时后，此文件会被第一次 gitlab-ctl reconfigure 自动删除，因此若使用随机密码登录，建议安装成功初始登录成功之后，立即修改初始密码）。


```
$ sudo cat /etc/gitlab/initial_root_password
# WARNING: This value is valid only in the following conditions
#          1. If provided manually (either via `GITLAB_ROOT_PASSWORD` environment variable or via `gitlab_rails['initial_root_password']` setting in `gitlab.rb`, it was provided before database was seeded for the first time (usually, the first reconfigure run).
#          2. Password hasn't been changed manually, either via UI or via command line.
#
#          If the password shown here doesn't work, you must reset the admin password following https://docs.gitlab.com/ee/security/reset_user_password.html#reset-your-root-password.

Password: sWmGpTw6tA01pWhz+qvNbVJwzGQxLBg84wxyG5yZYKc=

# NOTE: This file will be automatically deleted in the first reconfigure run after 24 hours.
```

访问<http://192.168.1.71>，使用上述密码和用户名 root 登录。


## 查看当前GitLab版本

确保源版本和目标版本一致。

```
cat /opt/gitlab/embedded/service/gitlab-rails/VERSION
```


安装时可指定版本：

```
sudo apt-get install gitlab-ce=16.7.0-ce.0
```

注意更新的路径

```
yum install gitlab-ce-16.3.0

yum install gitlab-ce-16.7.0
```

## GitLab备份



升级前先备份。
备份时需要保持GitLab处于正常运行状态，通过执行以下命令进行备份：

```
gitlab-rake gitlab:backup:create
```


默认备份文件“1704260742_2024_01_03_16.7.0”会存在在`/var/opt/gitlab/backups`目录下，备份的文件是个tar包，包含了GitLab的所有数据(账户、仓库等)。

同时需要手动备份 `/etc/gitlab/gitlab-secrets.json` 、`/etc/gitlab/gitlab.rb`。


## 恢复（在目标GitLab服务器执行）

停止目标GitLab数据服务：

```
sudo gitlab-ctl stop puma
sudo gitlab-ctl stop sidekiq
# 验证
sudo gitlab-ctl status
```


拷贝源gitlab的备份文件、`gitlab-secrets.json` 、`gitlab.rb`到目标服务器。


执行

```
gitlab-ctl reconfigure
```

执行恢复操作（不加后缀或者加着都行）：

```
sudo gitlab-backup restore BACKUP=1704260742_2024_01_03_16.7.0
```


注意看着电脑，会提示输入yes


恢复完成重启服务并检查：

```
sudo gitlab-ctl restart
sudo gitlab-rake gitlab:check SANITIZE=true
```

检查：

```
sudo gitlab-rake gitlab:doctor:secrets

sudo gitlab-rake gitlab:artifacts:check
sudo gitlab-rake gitlab:lfs:check
sudo gitlab-rake gitlab:uploads:check
```

## 停服


源服务器停止：

```
sudo gitlab-ctl stop
```

## 卸载


查询 gitlab 安装包


```
dpkg --list |grep gitlab

ii  gitlab-ce                             16.7.0-ce.0                             amd64        GitLab Community Edition (including NGINX, Postgres, Redis)
```

```
ps -ef | grep gitlab
```


卸载 gitlab

```
sudo apt-get --purge remove gitlab-ce
```

删除所有包含gitlab文件

```
sudo find / -name gitlab | xargs rm -rf
sudo find / -name gitlab | xargs sudo rm -rf
```


确认一下

```
ps -ef | grep gitlab
dpkg --list |grep gitlab
```



## 参考

<https://docs.gitlab.com/ee/raketasks/backup_restore.html#restore-for-omnibus-gitlab-installations>