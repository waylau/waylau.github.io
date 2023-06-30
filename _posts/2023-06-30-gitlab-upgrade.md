---
layout: post
title: GitLab升级版本
date: 2023-06-30 00:22
author: admin
comments: true
categories: [GitLab]
tags: [GitLab,Git]
---

GitLab是开源的项目管理和代码托管平台。本节介绍如何升级。

<!-- more -->


## GitLab配置文件

所在位置

```
/etc/gitlab/gitlab.rb
```


## GitLab安装包目录

所在位置

```
/opt/gitlab
```


## 查看当前GitLab版本

```
[root@localhost backups]# cat /opt/gitlab/embedded/service/gitlab-rails/VERSION              

15.10.0
```


## GitLab备份

升级前先备份。

备份时需要保持GitLab处于正常运行状态，通过执行`gitlab-rake gitlab:backup:create`进行备份
默认备份文件会存在在/var/opt/gitlab/backups目录下，备份的文件是个tar包，包含了GitLab的所有数据(账户、仓库等)

```
[root@localhost backups]# gitlab-rake gitlab:backup:create                                                 
2023-06-29 07:51:15 UTC -- Dumping database ...                                                            
Dumping PostgreSQL database gitlabhq_production ... [DONE]                                                 
2023-06-29 07:51:19 UTC -- Dumping database ... done                                                       
2023-06-29 07:51:19 UTC -- Dumping repositories ...       

// ...为节约篇幅，此处省略输出内容

2023-06-29 07:51:21 UTC -- Deleting tar staging files ... done                                             
2023-06-29 07:51:21 UTC -- Deleting backups/tmp ...                                                        
2023-06-29 07:51:21 UTC -- Deleting backups/tmp ... done                                                   
2023-06-29 07:51:21 UTC -- Warning: Your gitlab.rb and gitlab-secrets.json files contain sensitive data    
and are not included in this backup. You will need these files to restore a backup.                        
Please back them up manually.                                                                              
2023-06-29 07:51:21 UTC -- Backup 1688025075_2023_06_29_15.10.0 is done.                                   
2023-06-29 15:51:21 +0800 -- Deleting backup and restore lock file      
```



备份成功，可以看到备份目录文件`1688025075_2023_06_29_15.10.0_gitlab_backup.tar`。



同时需要手动备份 `/etc/gitlab/gitlab-secrets.json` 、`/etc/gitlab/gitlab.rb`



## 添加GitLab的yum源仓库


```
[root@localhost ~]# curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh
 | sudo bash                                                                                               
Detected operating system as centos/7.                                                                     
Checking for curl...                                                                                       
Detected curl...                                                                                           
Downloading repository file: https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/config_file.
repo?os=centos&dist=7&source=script                                                                        
done.                                                                                                      
Installing pygpgme to verify GPG signatures...                                                             
已加载插件：fastestmirror, langpacks                                                                       
Loading mirror speeds from cached hostfile                                                                 
 * base: mirrors.jlu.edu.cn                                                                                
 * extras: mirrors.jlu.edu.cn                                                                              
 * updates: mirrors.jlu.edu.cn                                                                             
gitlab_gitlab-ce-source/signature                                                   |  862 B  00:00:00     
gitlab_gitlab-ce-source/signature                                                   |  951 B  00:00:00 !!! 
软件包 pygpgme-0.3-9.el7.x86_64 已安装并且是最新版本                                                       
无须任何处理                                                                                               
Installing yum-utils...                                                                                    
已加载插件：fastestmirror, langpacks                                                                       
Loading mirror speeds from cached hostfile                                                                 
 * base: mirrors.jlu.edu.cn                                                                                
 * extras: mirrors.jlu.edu.cn                                                                              
 * updates: mirrors.jlu.edu.cn                                                                             
软件包 yum-utils-1.1.31-54.el7_8.noarch 已安装并且是最新版本                                               
无须任何处理                                                                                               
Generating yum cache for gitlab_gitlab-ce...                                                               
Generating yum cache for gitlab_gitlab-ce-source...                                                        
                                                                                                           
The repository is setup! You can now install packages.  
```

## GitLab升级路径


每个版本都有升级路径，升级时需要按照路径来升级：

```
GitLab 8: 8.11.Z > 8.12.0 > 8.17.7

GitLab 9: 9.0.13 > 9.5.10

GitLab 10: 10.0.7 > 10.8.7

GitLab 11: 11.0.6 > 11.11.8

GitLab 12: 12.0.12 > 12.1.17 > 12.10.14

GitLab 13: 13.0.14 > 13.1.11 > 13.8.8 > 13.12.15

GitLab 14: 14.0.12 > 14.3.6 > 14.9.5 > 14.10.5

GitLab 15: 15.0.5 > 15.1.6 (for GitLab instances with multiple web nodes) > 15.4.6 > 15.11.x

GitLab 16: latest 16.Y.Z
```

如果不按路径，直接升级到最新版本，你会得到如下错误：

```
[root@localhost ~]# sudo yum install gitlab-ce                                                             
已加载插件：fastestmirror, langpacks                                                                       
Loading mirror speeds from cached hostfile                                                                 
 * base: mirrors.jlu.edu.cn                                                                                
 * extras: mirrors.jlu.edu.cn                                                                              
 * updates: mirrors.jlu.edu.cn                                                                             
gitlab_gitlab-ce/x86_64/signature                                                   |  862 B  00:00:00     
gitlab_gitlab-ce/x86_64/signature                                                   | 1.0 kB  00:00:00 !!! 
gitlab_gitlab-ce-source/signature                                                   |  862 B  00:00:00     
gitlab_gitlab-ce-source/signature                                                   |  951 B  00:00:00 !!! 
正在解决依赖关系                                                                                           
There are unfinished transactions remaining. You might consider running yum-complete-transaction, or "yum-c
omplete-transaction --cleanup-only" and "yum history redo last", first to finish them. If those don't work 
you'll have to try removing/installing packages by hand (maybe package-cleanup can help).                  
--> 正在检查事务                                                                                           
---> 软件包 gitlab-ce.x86_64.0.15.10.0-ce.0.el7 将被 升级                                                  
---> 软件包 gitlab-ce.x86_64.0.16.1.0-ce.0.el7 将被 更新                                                   
--> 解决依赖关系完成                                                                                       
                                                                                                           
依赖关系解决                                                                                               
                                                                                                           
===========================================================================================================
 Package               架构               版本                          源                            大小 
===========================================================================================================
正在更新:                                                                                                  
 gitlab-ce             x86_64             16.1.0-ce.0.el7               gitlab_gitlab-ce             1.2 G 
                                                                                                           
事务概要                                                                                                   
===========================================================================================================
升级  1 软件包                                                                                             
                                                                                                           
总下载量：1.2 G                                                                                            
Is this ok [y/d/N]: y                                                                                      
Downloading packages:                                                                                      
No Presto metadata available for gitlab_gitlab-ce                                                          
gitlab-ce-16.1.0-ce.0.el7.x86_64.rpm                                                | 1.2 GB  00:03:24     
Running transaction check                                                                                  
Running transaction test                                                                                   
Transaction test succeeded                                                                                 
Running transaction                                                                                        
gitlab preinstall: It seems you are upgrading from major version 15 to major version 16.                   
gitlab preinstall: It is required to upgrade to the latest 15.11.x version first before proceeding.        
gitlab preinstall: Please follow the upgrade documentation at https://docs.gitlab.com/ee/update/index.html#
upgrade-paths                                                                                              
error: %pre(gitlab-ce-16.1.0-ce.0.el7.x86_64) scriptlet failed, exit status 1                              
Error in PREIN scriptlet in rpm package gitlab-ce-16.1.0-ce.0.el7.x86_64                                   
gitlab-ce-15.10.0-ce.0.el7.x86_64 was supposed to be removed but is not!                                   
  验证中      : gitlab-ce-15.10.0-ce.0.el7.x86_64                                                      1/2 
  验证中      : gitlab-ce-16.1.0-ce.0.el7.x86_64                                                       2/2 
                                                                                                           
失败:                                                                                                      
  gitlab-ce.x86_64 0:15.10.0-ce.0.el7                  gitlab-ce.x86_64 0:16.1.0-ce.0.el7                  
                                                                                                           
完毕！     
```



因此，15.10.0要先升级到15.11.1版本：

```
[root@localhost ~]# sudo yum install gitlab-ce-15.11.1-ce.0.el7.x86_64
已加载插件：fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.jlu.edu.cn
 * extras: mirrors.jlu.edu.cn
 * updates: mirrors.jlu.edu.cn
gitlab_gitlab-ce/x86_64/signature                                                   |  862 B  00:00:00     
gitlab_gitlab-ce/x86_64/signature                                                   | 1.0 kB  00:00:00 !!! 
gitlab_gitlab-ce-source/signature                                                   |  862 B  00:00:00     
gitlab_gitlab-ce-source/signature                                                   |  951 B  00:00:00 !!! 
正在解决依赖关系
There are unfinished transactions remaining. You might consider running yum-complete-transaction, or "yum-complete-transaction --cleanup-only" and "yum history redo last", first to finish them. If those don't work you'll have to try removing/installing packages by hand (maybe package-cleanup can help).
--> 正在检查事务
---> 软件包 gitlab-ce.x86_64.0.15.10.0-ce.0.el7 将被 升级
---> 软件包 gitlab-ce.x86_64.0.15.11.1-ce.0.el7 将被 更新
--> 解决依赖关系完成

依赖关系解决

=========================================================================================================== Package               架构               版本                          源                            大小
===========================================================================================================正在更新:
 gitlab-ce             x86_64             15.11.1-ce.0.el7              gitlab_gitlab-ce             1.2 G

事务概要
===========================================================================================================升级  1 软件包

总下载量：1.2 G
Is this ok [y/d/N]: y


// ...为节约篇幅，此处省略输出内容


[2023-06-29T19:15:32+08:00] WARN: This release of Cinc Client became end of life (EOL) on May 1st 2023. Please update to a supported release to receive new features, bug fixes, and security updates.
gitlab Reconfigured!
Restarting previously running GitLab services
ok: run: alertmanager: (pid 57045) 1s
ok: run: gitaly: (pid 60007) 8415614s, got TERM
ok: run: gitlab-exporter: (pid 57043) 1s
ok: run: gitlab-kas: (pid 56933) 2s
ok: run: gitlab-workhorse: (pid 56951) 2s
ok: run: logrotate: (pid 57081) 0s
ok: run: nginx: (pid 57088) 1s
ok: run: node-exporter: (pid 57158) 0s
ok: run: postgres-exporter: (pid 57164) 0s
ok: run: postgresql: (pid 58275) 7788188s
ok: run: prometheus: (pid 57175) 1s
ok: run: puma: (pid 57182) 0s
ok: run: redis: (pid 56672) 68s
ok: run: redis-exporter: (pid 57204) 1s
ok: run: sidekiq: (pid 57213) 0s

     _______ __  __          __
    / ____(_) /_/ /   ____ _/ /_
   / / __/ / __/ /   / __ `/ __ \
  / /_/ / / /_/ /___/ /_/ / /_/ /
  \____/_/\__/_____/\__,_/_.___/
  

Upgrade complete! If your GitLab server is misbehaving try running
  sudo gitlab-ctl restart
before anything else.
If you need to roll back to the previous version you can use the database
backup made during the upgrade (scroll up for the filename).

  验证中      : gitlab-ce-15.11.1-ce.0.el7.x86_64                                                      1/2 
  验证中      : gitlab-ce-15.10.0-ce.0.el7.x86_64                                                      2/2 

更新完毕:
  gitlab-ce.x86_64 0:15.11.1-ce.0.el7                                                                      

完毕！
```


而后再更新到最新版本：

```
[root@localhost ~]# sudo yum install gitlab-ce      

// ...为节约篇幅，此处省略输出内容

     _______ __  __          __                                                                            
    / ____(_) /_/ /   ____ _/ /_                                                                           
   / / __/ / __/ /   / __ `/ __ \                                                                          
  / /_/ / / /_/ /___/ /_/ / /_/ /                                                                          
  \____/_/\__/_____/\__,_/_.___/                                                                           
                                                                                                           
                                                                                                           
Upgrade complete! If your GitLab server is misbehaving try running                                         
  sudo gitlab-ctl restart                                                                                  
before anything else.                                                                                      
If you need to roll back to the previous version you can use the database                                  
backup made during the upgrade (scroll up for the filename).                                               
                                                                                                           
  验证中      : gitlab-ce-16.1.1-ce.0.el7.x86_64                                                       1/2 
  验证中      : gitlab-ce-15.11.1-ce.0.el7.x86_64                                                      2/2 
                                                                                                           
更新完毕:                                                                                                  
  gitlab-ce.x86_64 0:16.1.1-ce.0.el7                                                                       
                                                                                                           
完毕！    
```
