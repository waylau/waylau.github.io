---
layout: post
title: 使用Podman对FileZilla进行容器化部署
date: 2023-12-10 00:22
author: admin
comments: true
categories: [Podman,FileZilla]
tags: [Podman,FileZilla]
---


本文演示了如何在Linux（以Ubuntu为例）安装Podman，并通过Podman部署FileZilla。

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
# 例：使用 podman pull registry.access.redhat.com/ubi8-minimal 时，
# 仅仅会从registry.access.redhat.com去获取镜像。
# 如果直接使用 podman pull ubuntu 时，没有明确指明仓库的时候，使用以下配置的仓库顺序去获取
unqualified-search-registries = ["docker.io", "registry.access.redhat.com"]
 
# 配置仓库的地址，可以直接在location里配置国内镜像例如：docker.mirrors.ustc.edu.cn
# 直接在location里配置的时候，可以不需要后面的 [[registry.mirror]] 内容，
# 但是这样只能配置一个镜像地址，这个镜像挂了就没法尝试其它镜像。
# prefix的值与unqualified-search-registries里配置的得一样，但是可以支持通配符。
# prefix不写的情况下，默认与location的指一样。
[[registry]]
prefix = "docker.io"
location = "mirror.baidubce.com"
insecure = true
 
# 在这里可以配置多个镜像地址，前提是至少有一个[[registry]]配置。
# 需要注意的是，无论 unqualified-search-registries 选择了哪个仓库，
# 都会先从这里的上下顺序开始去拉取镜像，最后才会去匹配上[[registry]]里prefix对应的location位置拉取镜像。
# 所以这里需要注意，上面配置的不同仓库类型，这里配置的镜像并不能是通用的，所以 unqualified-search-registries 配置了多个仓库的时候，就最好直接使用[[registry]] 的 location 指定镜像地址，不要配置 [[registry.mirror]] 了。
# redhat 的国内镜像暂未发现。
[[registry.mirror]]
location = "hub-mirror.c.163.com"
insecure = true
[[registry.mirror]]
location = "ccr.ccs.tencentyun.com"
insecure = true
[[registry.mirror]]
location = "dockerproxy.com"
insecure = true
# 当使用 podman pod create 命令时候，因需要从k8s.gcr.io拉取 pause 镜像，但是该站点在国内被墙了。
# 所以给该站点搞个镜像。以下镜像是阿里云第三方用户，非官方。
# 或者 registry.aliyuncs.com/googlecontainersmirror ，也是第三方用户。
# 目前没找到国内官方的镜像。gcr.mirrors.ustc.edu.cn 返回403不能用了
[[registry]]
prefix = "k8s.gcr.io"
location = "registry.aliyuncs.com/google_containers"
insecure = true
```

## Podman搜索、下载image（以FileZilla为例）


执行`podman search`命令搜索image：


```
$ podman search filezilla
INDEX       NAME                                        DESCRIPTION                                      STARS       OFFICIAL    AUTOMATED
docker.io   docker.io/kasmweb/filezilla                 FileZilla for Kasm Workspaces                    8                       
docker.io   docker.io/linuxserver/filezilla                                                              39                      
docker.io   docker.io/jlesage/filezilla                 Docker container for FileZilla                   64                      [OK]
docker.io   docker.io/sparklyballs/filezilla                                                             0                       [OK]
docker.io   docker.io/patricol/filezilla                VNC filezilla                                    1                       [OK]
docker.io   docker.io/yantis/filezilla                  Filezilla FTP Client - local or remote over ...  2                       [OK]
docker.io   docker.io/cluebiz/filezillaserver           FileZilla Client is a fast and reliable cros...  0                       
docker.io   docker.io/tsari/filezilla                   Dockerized filezilla                             0                       [OK]                    
```                                                                                            



## 拉取image

```
$ podman pull docker.io/jlesage/filezilla:latest
```






## 创建FileZilla挂载路径


创建目录并授权：

```
sudo mkdir -p -m 777 /data/filezilla/container-filezilla-ftp/ /data/ftp/
```


给数据目录授权：


```
sudo chown 165562:165562 /data/filezilla/container-filezilla-ftp/ /data/ftp/
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
$ podman run --cpus=2 --memory=4g -e TZ="Asia/Shanghai"  -e   ENABLE_CJK_FONT=1  --restart=always   -p 5801:5800 --name=container-filezilla-ftp   --privileged=true -v /data/filezilla/container-filezilla-ftp:/config:rw     -v /data/ftp:/storage:rw    docker.io/jlesage/filezilla:latest
```

* `/data/filezilla`: 应用的configuration, states, log 等文件
* `/data/ftp`: FTP文件目录
* ENABLE_CJK_FONT: 可选项，设置为 1 时，将安装开源字体 WenQuanYi Zen Hei 。该字体包含各种中文/日文/韩文字符


## 开放端口

开放 5801 端口

```
sudo ufw allow 5801
```

## 浏览器访问


访问：<http://192.168.1.70:5801>
