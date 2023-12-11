---
layout: post
title: 使用Podman对Redis进行容器化部署
date: 2023-12-09 00:22
author: admin
comments: true
categories: [Podman,Redis]
tags: [Podman,Redis]
---


本文演示了如何在Linux（以Ubuntu为例）安装Podman，并通过Podman部署Redis。

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

## Podman搜索、下载image（以Redis为例）


执行`podman search`命令搜索image：


```
$ podman search redis
INDEX       NAME                                                          DESCRIPTION                                      STARS       OFFICIAL    AUTOMATED
docker.io   docker.io/library/redis                                       Redis is an open source key-value store that...  12538       [OK]        
docker.io   docker.io/redislabs/redisearch                                Redis With the RedisSearch module pre-loaded...  61                      
docker.io   docker.io/redislabs/redisinsight                              RedisInsight - The GUI for Redis                 94                      
docker.io   docker.io/redis/redis-stack-server                            redis-stack-server installs a Redis server w...  62                      
docker.io   docker.io/redis/redis-stack                                   redis-stack installs a Redis server with add...  85                      
docker.io   docker.io/redislabs/rebloom                                   A probablistic datatypes module for Redis        25                      [OK]
docker.io   docker.io/redislabs/redis                                     Clustered in-memory database engine compatib...  40                      
docker.io   docker.io/redislabs/rejson                                    RedisJSON - Enhanced JSON data type processi...  53                      
docker.io   docker.io/redislabs/redisgraph                                A graph database module for Redis                26                      [OK]
docker.io   docker.io/redislabs/redismod                                  An automated build of redismod - latest Redi...  43                      [OK]
```                                                                                            



执行`podman pull`命令拉取最新的Redis image：

```
$ podman pull docker.io/library/redis:7.2.3
Trying to pull docker.io/library/redis:7.2.3...
Getting image source signatures
Copying blob 4f4fb700ef54 done  
Copying blob 127cd75a68a2 done  
Copying blob 3c6368585bf1 done  
Copying blob ac88aa9d4021 done  
Copying blob 1f7ce2fa46ab done  
Copying blob 3911d271d7d8 done  
Copying blob f3993c1104fc done  
Copying config 961dda256b done  
Writing manifest to image destination
Storing signatures
961dda256baa7a35297d34cca06bc2bce8397b0ef8b68d8064c30e338afc5a7d
```


执行`podman images`命令查看下载的image：

```
$ podman images                                                                  
REPOSITORY                                            TAG         IMAGE ID      CREATED      SIZE          
docker.io/library/redis                               7.2.3       961dda256baa  4 weeks ago  142 MB
```


执行`podman rmi`命令删除image：

```
$ podman rmi 961dda256baa  
Untagged: docker.io/library/redis:7.2.3                                                            
Deleted: 331fb4361a5db026d151d33331b53f809a92270fb2704ed5ac74e52ae4df1128    
```





## 准备Redis配置文件

准备好Redis的配置文件`container-engine-redis.conf`到`/etc/`目录下


```
sudo mv container-engine-redis.conf /etc/
```



## 创建Redis挂载路径


创建目录并授权：

```
sudo mkdir -p -m 777 /data/redis/container-engine-redis/
```


给数据目录授权：


```
sudo chown 165562:165562 /data/redis/container-engine-redis/
```


## 给CPU、内存的权限（可选）

给CPU、内存的权限：


创建delegate.conf 内容如下：


```
[Service]
Delegate=memory pids cpu cpuset
```

```
sudo mkdir -p /etc/systemd/system/user@.service.d/

sudo mv delegate.conf /etc/systemd/system/user@.service.d/
```

重启系统，或者执行下面命令生效：



```
sudo systemctl daemon-reload
```


## 运行容器

为运行容器：

```
$ podman run --cpus=4 --memory=8g -e TZ="Asia/Shanghai" --restart=always   -p 6380:6379 --name=container-engine-redis  --privileged=true -v /etc/container-engine-redis.conf:/etc/redis/redis.conf -v /data/redis/container-engine-redis:/data    -d    docker.io/library/redis:7.2.3 redis-server    /etc/redis/redis.conf --requirepass yourpasswd
b547b1ca57b86be27d8f2cbb84a0a6c12d5976dfe46d9972a42552039a8c3541
```


## 开放端口

开放 6391 端口

```
sudo ufw allow 6391
```

## 进入容器执行命令

```
podman exec -it container-engine-redis redis-cli -p 6379
```

登录Redis执行命令：

```
$ podman exec -it container-engine-redis redis-cli -p 6379
127.0.0.1:6380> config get requirepass
(error) NOAUTH Authentication required.
127.0.0.1:6380> auth yourpasswd
OK
127.0.0.1:6380> config get requirepass
1) "requirepass"
2) "yourpasswd"
127.0.0.1:6380> 
```