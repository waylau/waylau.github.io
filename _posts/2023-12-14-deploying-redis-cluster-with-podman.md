---
layout: post
title: 使用Podman对Redis集群进行容器化部署
date: 2023-12-14 00:22
author: admin
comments: true
categories: [Podman,Redis]
tags: [Podman,Redis]
---


本文演示了如何在Linux（以Ubuntu为例）安装Podman，并通过Podman部署Redis集群。

<!-- more -->

## Podman的安装、使用

Podman的安装和基本使用，参见前文[《使用Podman对MySQL进行容器化部署》](https://waylau.com/deploying-mysql-server-with-podman/)。

Redis单机版的部署，参见<https://waylau.com/deploying-redis-with-podman/>。


## 拉取image

```
$ podman pull docker.io/library/redis:7.2.3
```





## 创建Redis挂载路径、配置文件


创建目录并授权、创建配置文件：

```
for port in $(seq 6380 6385); 
do 
sudo mkdir -p -m 777 /data/redis/container-themetis-engine-redis-cluster/node-${port}/conf
touch /data/redis/container-themetis-engine-redis-cluster/node-${port}/conf/redis.conf
cat  << EOF > /data/redis/container-themetis-engine-redis-cluster/node-${port}/conf/redis.conf
port ${port}
appendonly yes
cluster-enabled yes 
cluster-config-file nodes.conf
cluster-node-timeout 15000
cluster-announce-ip 192.168.1.70
cluster-announce-port ${port}
cluster-announce-bus-port 1${port}
EOF
done
```

命令解释：

* port：节点端口；
* appendonly：是否开启 AOF 持久化模式，默认 no；
* cluster-enabled：是否开启集群模式，默认 no；
* cluster-config-file：集群节点信息文件；
* cluster-node-timeout：集群节点连接超时时间；
* cluster-announce-ip：集群节点 IP
* cluster-announce-port：集群节点映射端口；
* cluster-announce-bus-port：集群节点总线端口。


给数据目录授权：


```
sudo chown 165562:165562 -R /data/redis/container-themetis-engine-redis-cluster/
```



## 运行容器

运行容器：



```
$ for port in $(seq 6380 6385); 
do 
   podman run -it -d -p ${port}:${port} -p 1${port}:1${port}  --cpus=1 --memory=32g -e TZ="Asia/Shanghai" --restart always --name=container-themetis-engine-redis-${port}  --privileged=true -v /data/redis/container-themetis-engine-redis-cluster/node-${port}/conf/redis.conf:/etc/redis/redis.conf --privileged=true -v /data/redis/container-themetis-engine-redis-cluster/node-${port}:/data   docker.io/library/redis:7.2.3 redis-server /etc/redis/redis.conf --requirepass yourpasswd
done

f7f4f97e564fc4756d7559989e946d1fdde11302b58c50933d90415af1f5e31d
c11b59bd37744e84a4c0b3cf3f5bcc08eac8bcefe618b3e8563c260c3c058f73
27c1fb16231210f088b499b6602d9434cd15446dcad030a568602d3a107ed7dc
6d5382cccef62dad289aea7d0dabf6da856c408a38d3906a2b7ec856cf27aae0
90a0f7ccd4dbefc448a0f7f91b7b8d61fa79d16cb2fca4f4d959547c35280caf
dc5e1d176dc539d4856a5706dc6df43a0c341e37444e6fc630002a584d5bdb26
```



## 开放端口

开放 6380:6385  和  16380:16385 端口

```
for port in $(seq 6380 6385); 
do 
sudo ufw allow ${port} 
sudo ufw allow 1${port}
done
```

## 进入容器创建集群


登录Redis集群中的任一实例：

```
podman exec -it container-themetis-engine-redis-6380 /bin/bash
```

登录Redis执行命令：


```
redis-cli  -a yourpasswd --cluster create 192.168.1.70:6380 192.168.1.70:6381 192.168.1.70:6382 192.168.1.70:6383 192.168.1.70:6384 192.168.1.70:6385  --cluster-replicas 1
```

输出如下：

```
root@4542e510d098:/data# redis-cli  -a yourpasswd --cluster create 192.168.1.70:6380 192.168.1.70:6381 192.168.1.70:6382 192.168.1.70:6383 192.168.1.70:6384 192.168.1.70:6385  --cluster-replicas 1
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 192.168.1.70:6384 to 192.168.1.70:6380
Adding replica 192.168.1.70:6385 to 192.168.1.70:6381
Adding replica 192.168.1.70:6383 to 192.168.1.70:6382
>>> Trying to optimize slaves allocation for anti-affinity
[WARNING] Some slaves are in the same host as their master
M: 976d62c6ce881cbaa23f8528bf834155358c4acc 192.168.1.70:6380
   slots:[0-5460] (5461 slots) master
M: c5b1cf2cfb425058e12697713e4f789842059bf1 192.168.1.70:6381
   slots:[5461-10922] (5462 slots) master
M: bb6a6d71208708296f36477c5df89652576d4f3d 192.168.1.70:6382
   slots:[10923-16383] (5461 slots) master
S: 48b32aa8464a980c096978d1479f68d2b45459a7 192.168.1.70:6383
   replicates c5b1cf2cfb425058e12697713e4f789842059bf1
S: 69f448b02fd9bdc7721f409b148f6d719a7a00f2 192.168.1.70:6384
   replicates bb6a6d71208708296f36477c5df89652576d4f3d
S: 669cd2445a4a2984ee64471594ac6fc1b53acde4 192.168.1.70:6385
   replicates 976d62c6ce881cbaa23f8528bf834155358c4acc
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
...
>>> Performing Cluster Check (using node 192.168.1.70:6380)
M: 976d62c6ce881cbaa23f8528bf834155358c4acc 192.168.1.70:6380
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: 48b32aa8464a980c096978d1479f68d2b45459a7 192.168.1.70:6383
   slots: (0 slots) slave
   replicates c5b1cf2cfb425058e12697713e4f789842059bf1
S: 669cd2445a4a2984ee64471594ac6fc1b53acde4 192.168.1.70:6385
   slots: (0 slots) slave
   replicates 976d62c6ce881cbaa23f8528bf834155358c4acc
M: c5b1cf2cfb425058e12697713e4f789842059bf1 192.168.1.70:6381
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
M: bb6a6d71208708296f36477c5df89652576d4f3d 192.168.1.70:6382
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: 69f448b02fd9bdc7721f409b148f6d719a7a00f2 192.168.1.70:6384
   slots: (0 slots) slave
   replicates bb6a6d71208708296f36477c5df89652576d4f3d
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

可以看到创建了三主三从的集群。



## 查看节点相关信息


进入容器后，通过以下命令进入Redis：



```
root@4542e510d098:/data# redis-cli -p 6380 -a yourpasswd
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
```

输入cluster info 查看集群信息：

```
127.0.0.1:6380> cluster info
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6
cluster_size:3
cluster_current_epoch:6
cluster_my_epoch:1
cluster_stats_messages_ping_sent:424
cluster_stats_messages_pong_sent:412
cluster_stats_messages_sent:836
cluster_stats_messages_ping_received:407
cluster_stats_messages_pong_received:424
cluster_stats_messages_meet_received:5
cluster_stats_messages_received:836
total_cluster_links_buffer_limit_exceeded:0
```


输入cluster nodes 查看所有节点相关信息：

```
127.0.0.1:6380> cluster nodes
48b32aa8464a980c096978d1479f68d2b45459a7 192.168.1.70:6383@16383 slave c5b1cf2cfb425058e12697713e4f789842059bf1 0 1702540715000 2 connected
976d62c6ce881cbaa23f8528bf834155358c4acc 192.168.1.70:6380@16380 myself,master - 0 1702540716000 1 connected 0-5460
669cd2445a4a2984ee64471594ac6fc1b53acde4 192.168.1.70:6385@16385 slave 976d62c6ce881cbaa23f8528bf834155358c4acc 0 1702540716000 1 connected
c5b1cf2cfb425058e12697713e4f789842059bf1 192.168.1.70:6381@16381 master - 0 1702540717000 2 connected 5461-10922
bb6a6d71208708296f36477c5df89652576d4f3d 192.168.1.70:6382@16382 master - 0 1702540717773 3 connected 10923-16383
69f448b02fd9bdc7721f409b148f6d719a7a00f2 192.168.1.70:6384@16384 slave bb6a6d71208708296f36477c5df89652576d4f3d 0 1702540718777 3 connected
```



## 测试

先在 6380 节点存入数据：

```
127.0.0.1:6380> set testkey loveyou
OK
```

先后在其他节点查询数据，如果能查到即代表集群搭建成功：



```
root@2e2a369ee06b:/data# redis-cli -c -p 6384 -a yourpasswd
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
127.0.0.1:6384> get testkey
-> Redirected to slot [4757] located at 192.168.1.70:6380
"loveyou"
```

## 参考

<https://cloud.tencent.com/developer/article/2145671>