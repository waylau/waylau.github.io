---
layout: post
title: 使用Podman对vsftpd进行容器化部署
date: 2023-12-12 00:22
author: admin
comments: true
categories: [Podman,vsftpd]
tags: [Podman,vsftpd]
---


本文演示了如何在Linux（以Ubuntu为例）安装Podman，并通过Podman部署vsftpd。

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
## 拉取image



```
$ podman pull  docker.io/fauria/vsftpd:latest
```








## 创建Redis挂载路径


创建目录并授权：

```
sudo mkdir -p -m 777 /data/vsftpd/container-themetis-vsftpd-ftp
```


给数据目录授权：


```
sudo chown 165562:165562 /data/vsftpd/container-themetis-vsftpd-ftp
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
podman run -d  --cpus=2 --memory=2g -e TZ="Asia/Shanghai"  -e --restart=always --privileged=true -v /data/vsftpd/container-themetis-vsftpd-ftp:/home/vsftpd -p 20:20 -p 21:21 -p 21100-21110:21100-21110 -e FTP_USER=ftptest -e FTP_PASS=yourpasswd -e PASV_ADDRESS=192.168.1.70 -e PASV_MIN_PORT=21100 -e PASV_MAX_PORT=21110 --name=container-themetis-vsftpd-ftp --restart=always docker.io/fauria/vsftpd:latest
```



* `/data/vsftpd/container-themetis-vsftpd-ftp`: 应用的 configuration, states, log 等文件



## 添加用户



```
podman exec -i -t container-themetis-vsftpd-ftp  bash
echo -e "ftptest\yourpasswd" >> /etc/vsftpd/virtual_users.txt
/usr/bin/db_load -T -t hash -f /etc/vsftpd/virtual_users.txt /etc/vsftpd/virtual_users.db
mkdir -p -m 777 /home/vsftpd/ftptest
exit
podman restart container-themetis-vsftpd-ftp 
```







## 开放端口

开放 20、21 端口

```
sudo ufw allow 20
sudo ufw allow 21
sudo ufw allow  21100:21110/tcp
```

## 使用


最终`/data/vsftpd/container-themetis-vsftpd-ftp/ftptest` 是用于存放文件数据


## 问题

启动容器失败，保没有权限

```
$ podman start 14935b99c5bd
Error: unable to start container "14935b99c5bdc0206a05af764d4492f3534e99dbc27e7eeaf4db1d786d3c319c": rootlessport cannot expose privileged port 20, you can add 'net.ipv4.ip_unprivileged_port_start=20' to /etc/sysctl.conf (currently 1024), or choose a larger port number (>= 1024): listen tcp 0.0.0.0:20: bind: permission denied
```

原因是没有使用20端口的权限。

解决方法1：临时生效

```
sysctl net.ipv4.ip_unprivileged_port_start=0
```

解决方法2：永久生效

将`net.ipv4.ip_unprivileged_port_start=0`添加到`/etc/sysctl.conf`文件。
