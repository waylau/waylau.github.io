---
layout: post
title: 使用Podman对MySQL进行容器化部署
date: 2023-12-05 00:22
author: admin
comments: true
categories: [Podman,MySQL]
tags: [Podman,MySQL]
---


本文演示了如何在Linux（以Ubuntu为例）安装Podman，并通过Podman部署MySQL。

<!-- more -->

## 安装Podman


在 Ubuntu 20.10 版本以上安装执行以下命令：

```
sudo apt-get update
sudo apt-get -y install podman
```

执行`podman info`如下命令来查看Podman的信息：


```
$ podman info                                                                    
host:                                                                                                      
  arch: amd64                                                                                              
  buildahVersion: 1.23.1                                                                                   
  cgroupControllers:                                                                                       
  - memory                                                                                                 
  - pids                                                                                                   
  cgroupManager: systemd                                                                                   
  cgroupVersion: v2                                                                                        
  conmon:                                                                                                  
    package: 'conmon: /usr/bin/conmon'                                                                     
    path: /usr/bin/conmon                                                                                  
    version: 'conmon version 2.0.25, commit: unknown'                                                      
  cpus: 52                                                                                                 
  distribution:                                                                                            
    codename: jammy                                                                                        
    distribution: ubuntu                                                                                   
    version: "22.04"                                                                                       
  eventLogger: journald                                                                                    
  hostname: ubuntu-for-container                                                                           
  idMappings:                                                                                              
    gidmap:                                                                                                
    - container_id: 0                                                                                      
      host_id: 1001                                                                                        
      size: 1                                                                                              
    - container_id: 1                                                                                      
      host_id: 165536                                                                                      
      size: 65536                                                                                          
    uidmap:                                                                                                
    - container_id: 0                                                                                      
      host_id: 1001                                                                                        
      size: 1                                                                                              
    - container_id: 1                                                                                      
      host_id: 165536                                                                                      
      size: 65536                                                                                          
  kernel: 6.2.0-37-generic                                                                                 
  linkmode: dynamic                                                                                        
  logDriver: journald                                                                                      
  memFree: 187190579200                                                                                    
  memTotal: 192448438272                                                                                   
  ociRuntime:                                                                                              
    name: crun                                                                                             
    package: 'crun: /usr/bin/crun'                                                                         
    path: /usr/bin/crun                                                                                    
    version: |-                                                                                            
      crun version 0.17                                                                                    
      commit: 0e9229ae34caaebcb86f1fde18de3acaf18c6d9a                                                     
      spec: 1.0.0                                                                                          
      +SYSTEMD +SELINUX +APPARMOR +CAP +SECCOMP +EBPF +YAJL                                                
  os: linux                                                                                                
  remoteSocket:                                                                                            
    path: /run/user/1001/podman/podman.sock                                                                
  security:                                                                                                
    apparmorEnabled: false                                                                                 
    capabilities: CAP_CHOWN,CAP_DAC_OVERRIDE,CAP_FOWNER,CAP_FSETID,CAP_KILL,CAP_NET_BIND_SERVICE,CAP_SETFCA
P,CAP_SETGID,CAP_SETPCAP,CAP_SETUID,CAP_SYS_CHROOT                                                         
    rootless: true                                                                                         
    seccompEnabled: true                                                                                   
    seccompProfilePath: /usr/share/containers/seccomp.json                                                 
    selinuxEnabled: false                                                                                  
  serviceIsRemote: false                                                                                   
  slirp4netns:                                                                                             
    executable: /usr/bin/slirp4netns                                                                       
    package: 'slirp4netns: /usr/bin/slirp4netns'                                                           
    version: |-                                                                                            
      slirp4netns version 1.0.1                                                                            
      commit: 6a7b16babc95b6a3056b33fb45b74a6f62262dd4                                                     
      libslirp: 4.6.1                                                                                      
  swapFree: 2147479552                                                                                     
  swapTotal: 2147479552                                                                                    
  uptime: 20h 52m 9.41s (Approximately 0.83 days)                                                          
plugins:                                                                                                   
  log:                                                                                                     
  - k8s-file                                                                                               
  - none                                                                                                   
  - journald                                                                                               
  network:                                                                                                 
  - bridge                                                                                                 
  - macvlan                                                                                                
  volume:                                                                                                  
  - local                                                                                                  
registries: {}                                                                                             
store:                                                                                                     
  configFile: /home/lww/.config/containers/storage.conf                                                    
  containerStore:                                                                                          
    number: 0                                                                                              
    paused: 0                                                                                              
    running: 0                                                                                             
    stopped: 0                                                                                             
  graphDriverName: overlay                                                                                 
  graphOptions: {}                                                                                         
  graphRoot: /home/lww/.local/share/containers/storage                                                     
  graphStatus:                                                                                             
    Backing Filesystem: extfs                                                                              
    Native Overlay Diff: "true"                                                                            
    Supports d_type: "true"                                                                                
    Using metacopy: "false"                                                                                
  imageStore:                                                                                              
    number: 0                                                                                              
  runRoot: /run/user/1001/containers                                                                       
  volumePath: /home/lww/.local/share/containers/storage/volumes                                            
version:                                                                                                   
  APIVersion: 3.4.4                                                                                        
  Built: 0                                                                                                 
  BuiltTime: Thu Jan  1 08:00:00 1970                                                                      
  GitCommit: ""                                                                                            
  GoVersion: go1.18.1                                                                                      
  OsArch: linux/amd64                                                                                      
  Version: 3.4.4          
```

## 镜像源（仓库）配置


Podman 引入了一个 registry 的概念，
registry（注册表、登记处）就是多个容器镜像源（如，docker.io 就可以是其中之一）。没有指明镜像源，会使用配置文件中定义的镜像仓库。

podman 的镜像源配置文件为 `/etc/containers/registries.conf`，修改内容如下：

```
# 当 pull 一个镜像，没有指定镜像源的时候，下面字段指定的镜像源顺序获取
unqualified-search-registries = ["docker.io", "registry.access.redhat.com"]

# 设置镜像源
[[registry]]
prefix = "docker.io"
location = "2jriwsnk.mirror.aliyuncs.com"
```

## Podman搜索、下载image（以MySQL为例）


执行`podman search`命令搜索image：


```
$ podman search mysql                                                            
INDEX       NAME                                                  DESCRIPTION                              
        STARS       OFFICIAL    AUTOMATED                                                                  
docker.io   docker.io/library/mysql                               MySQL is a widely used, open-source relat
ion...  14654       [OK]                                                                                   
docker.io   docker.io/library/mariadb                             MariaDB Server is a high performing open 
sou...  5589        [OK]                                                                                   
docker.io   docker.io/library/percona                             Percona Server is a fork of the MySQL rel
ati...  622         [OK]                                                                                   
docker.io   docker.io/library/phpmyadmin                          phpMyAdmin - A web interface for MySQL an
d M...  902         [OK]                                                                                   
docker.io   docker.io/bitnami/mysql                               Bitnami MySQL Docker Image               
        104                     [OK]                                                                       
docker.io   docker.io/circleci/mysql                              MySQL is a widely used, open-source relat
ion...  29                                                                                                 
docker.io   docker.io/bitnami/mysqld-exporter 
```                                                                                            



执行`podman pull`命令拉取最新的MySQL image：

```
$ podman pull container-registry.oracle.com/mysql/community-server:latest        
Trying to pull container-registry.oracle.com/mysql/community-server:latest...                              
Getting image source signatures                                                                            
Copying blob e2c544ac83d3 done                                                                             
Copying blob fb0fc3e752fd done                                                                             
Copying blob 01809ac65afb done                                                                             
Copying blob 73e811415fb4 done                                                                             
Copying blob 986b43079e09 done                                                                             
Copying blob 27411f9a281c done                                                                             
Copying blob aa7113ff8c5d done                                                                             
Copying config 39654aa412 done                                                                             
Writing manifest to image destination                                                                      
Storing signatures                                                                                         
39654aa412c0a1fdaccac0d8072285676801507d4cd238d9a27577336b9b4d5d   
```


执行`podman images`命令查看下载的image：

```
$ podman images                                                                  
REPOSITORY                                            TAG         IMAGE ID      CREATED      SIZE          
container-registry.oracle.com/mysql/community-server  latest      39654aa412c0  4 weeks ago  612 MB 
```


执行`podman rmi`命令删除image：

```
$ podman rmi 39654aa412c0  
Untagged: container-registry.oracle.com/mysql/community-server:latest                                                             
Deleted: 331fb4361a5db026d151d33331b53f809a92270fb2704ed5ac74e52ae4df1128    
```

## 准备好MySQL的配置文件

准备好MySQL的配置文件`container-mysql-01.cnf`到/etc/目录下，文件名可以自定义，内容如下：

```
[mysqld]
user=mysql
character-set-server=utf8mb4
# 允许最大连接数
max_connections=1000
# 允许连接失败的次数
max_connect_errors=100
# 是否对sql语句大小写敏感，1表示不敏感
lower_case_table_names = 1
#MySQL默认的wait_timeout  值为8个小时, interactive_timeout参数需要同时配置才能生效
interactive_timeout = 1800
wait_timeout = 1800


# 以下配置按照特定容器配置，确保唯一
port=3307
```

其中，3307是自定义的端口。




```
sudo mv container-mysql-01.cnf /etc/
```



## 创建MySQL挂载路径


创建目录并授权：

```
sudo mkdir -p -m 777 /data/mysql/container-mysql-01/
```


给数据目录授权：


```
sudo chown 165562:165562 /data/mysql/container-mysql-01/
```


## 给CPU、内存的权限

给CPU、内存的权限：


创建delegate.conf内容如下：


```
[Service]
Delegate=memory pids cpu cpuset
```

移动文件到制定目录：

```
sudo mkdir -p /etc/systemd/system/user@.service.d/

sudo mv delegate.conf /etc/systemd/system/user@.service.d/
```

重启系统，或者执行下面命令生效：



```
sudo systemctl daemon-reload
```


## Podman运行容器

执行`podman run`命令运行容器：

```
podman run -itd  --cpus=4 --memory=8g -e TZ="Asia/Shanghai" -v /etc/container-mysql-01.cnf:/etc/my.cnf -p 3307:3307 --privileged=true --mount type=bind,src=/data/mysql/container-mysql-01,dst=/var/lib/mysql -e MYSQL_USER=admin -e MYSQL_PASSWORD=your_passwd -e MYSQL_ROOT_PASSWORD=your_passwd --restart=always    --name=container-mysql-01  39654aa412c0
```

上述命令分配的了CPU、内存资源，设置了环境变量，指定了数据存储目录，并且初始化了账号、密码。


## 开放端口

开放 3317端口

```
sudo ufw allow 3307
```

## 进入容器执行命令

```
podman exec -it container-mysql-01 /bin/bash   
```

登录MySQL

```
mysql -uroot -p     
```

给用户授权

```
GRANT SELECT, INSERT, UPDATE, ALTER, CREATE, DELETE, DROP, INDEX ON *.* TO admin;
```


## 参考

* <https://dev.mysql.com/doc/refman/8.2/en/linux-installation-docker.html>