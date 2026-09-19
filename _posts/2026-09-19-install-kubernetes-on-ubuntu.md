---
layout: post
title: 在Ubuntu中安装Kubernetes并使用GitLab CI进行部署
date: 2026-09-19 00:22
author: admin
comments: true
categories: [Kubernetes,Ubuntu]
tags: [Kubernetes,Ubuntu,GitLab]
---


本文总结了在Ubuntu中安装Kubernetes并使用GitLab CI进行部署的过程。



<!-- more -->


## 简单场景-只需要kubectl


```
sudo snap install kubectl --classic
kubectl version --client
```

## 集群场景-所有节点统一前置操作（必须全部执行）



### 先关闭swap（消除警告）

```bash
sudo swapoff -a
sudo sed -i '/swap/s/^/#/' /etc/fstab
```


### 启用 IPv4 数据包转发 

默认情况下，Linux 内核不允许 IPv4 数据包在接口之间路由。 大多数 Kubernetes 集群网络实现都会更改此设置（如果需要），但有些人可能希望管理员为他们执行此操作。 （有些人可能还期望设置其他 sysctl 参数、加载内核模块等；请参阅你的特定网络实施的文档。）

手动启用 IPv4 数据包转发：

```
# 设置所需的 sysctl 参数，参数在重新启动后保持不变
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.ipv4.ip_forward = 1
EOF

# 应用 sysctl 参数而不重新启动
sudo sysctl --system
```


使用以下命令验证 net.ipv4.ip_forward 是否设置为 1：

```
sysctl net.ipv4.ip_forward
```




### 安装kubelet、kubeadm、kubectl

更新apt软件包索引并安装使用Kubernetes apt仓库所需的软件包：

```
sudo apt-get update
# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
```

下载Kubernetes软件包仓库的公共签名密钥。所有仓库都使用相同的签名密钥，因此您可以忽略URL中的版本信息：

```
# If the directory `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
# sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.37/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```


添加相应的Kubernetes apt仓库：

```
# This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.37/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```


安装kubelet、kubeadm、kubectl：

```
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```


运行kubeadm前先启用kubelet服务：


```
sudo systemctl enable --now kubelet
```



### 重新生成 containerd 标准配置

```bash
# 备份原有配置
sudo cp /etc/containerd/config.toml /etc/containerd/config.toml.bak

# 生成默认配置并写入文件
sudo containerd config default | sudo tee /etc/containerd/config.toml
```

修改2个关键配置（国内镜像 + SystemdCgroup=true）

```bash
# 修改pause镜像为阿里云，开启systemd cgroup
sudo sed -i 's#registry.k8s.io/pause#registry.aliyuncs.com/google_containers/pause#g' /etc/containerd/config.toml
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
```


所有节点配置 containerd 支持 HTTP 私有仓库，比如 http `10.150.12.157:8084`。编辑 `/etc/containerd/config.toml`
找到 `[plugins.'io.containerd.cri.v1.images'.registry]`，添加：

```
[plugins.'io.containerd.cri.v1.images'.registry]
  config_path = ''

  [plugins.'io.containerd.cri.v1.images'.registry.mirrors]
    [plugins.'io.containerd.cri.v1.images'.registry.mirrors."10.150.12.157:8084"]
    endpoint = ["http://10.150.12.157:8084"]
  [plugins.'io.containerd.cri.v1.images'.registry.configs]
    [plugins.'io.containerd.cri.v1.images'.registry.configs."10.150.12.157:8084"]
    [plugins.'io.containerd.cri.v1.images'.registry.configs."10.150.12.157:8084".tls]
      insecure_skip_verify = true
```



### 重启 containerd

```bash
sudo systemctl daemon-reload
sudo systemctl restart containerd
sudo systemctl enable containerd
```

### 验证 containerd CRI 是否正常

```bash
# 安装crictl工具（可选，用于验证CRI）
sudo apt install cri-tools -y
sudo crictl info
```

### 清理旧集群拉取镜像

```bash
# 1. 清理旧集群
sudo kubeadm reset -f
sudo rm -rf /etc/kubernetes/ /var/lib/etcd/ /etc/cni/net.d/ $HOME/.kube/
sudo iptables -F && sudo iptables -t nat -F && sudo iptables -t mangle -F && sudo iptables -X
sudo systemctl restart containerd kubelet

# 2. 预拉镜像（已经拉过，可跳过；保险起见可以再跑一次）
sudo kubeadm config images pull \
--image-repository registry.aliyuncs.com/google_containers \
--kubernetes-version v1.37.0

# 3. 给pause镜像打官方标签（核心修复）
sudo ctr -n k8s.io images tag registry.aliyuncs.com/google_containers/pause:3.10.2 registry.k8s.io/pause:3.10.2
```

## Master 节点初始化项目


Master 节点初始化项目。执行你的init命令，**记得带上pod网络cidr和镜像仓库**（你刚才命令少了这两个参数，容易镜像拉取失败）

```bash
# 4. 执行kubeadm init
sudo kubeadm init \
--apiserver-advertise-address=10.150.12.160 \
--image-repository registry.aliyuncs.com/google_containers \
--kubernetes-version v1.37.0 \
--pod-network-cidr=192.168.0.0/16
```



输出如下内容：

```bash
kubeadm join 10.150.12.160:6443 --token xz4m2h.j1wp3f95f07d96u3 \
        --discovery-token-ca-cert-hash sha256:4f5c20e00e1c21e55aa10654fead6d638c141b656e038a9ae4250ff795ac3a2e         
```

这个是Worker节点加入集群命令，保存好，后续Worker节点接入集群需要使用。如果 token 过期，可以在 Master 上重新生成：

```bash
kubeadm token create --print-join-command
```


要使非 root 用户可以运行 kubectl，请运行以下命令配置 kubectl 权限， 它们也是 kubeadm init 输出的一部分：

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```


### 安装 Pod 网络插件（Calico）

**不装网络插件，节点永远 NotReady**。

Calico 是一个联网和网络策略供应商。 Calico 支持一套灵活的网络选项，因此你可以根据自己的情况选择最有效的选项，包括非覆盖和覆盖网络，带或不带 BGP。 Calico 使用相同的引擎为主机、Pod 和（如果使用 Istio 和 Envoy）应用程序在服务网格层执行网络策略。


下载原始 yaml 并执行：



```
curl -O https://raw.githubusercontent.com/projectcalico/calico/v3.32.2/manifests/calico.yaml
kubectl apply -f calico.yaml
```


### 验证等待Calico Pod就绪


```bash
# 监控pod状态
kubectl get pods -n kube-system -w
# 全部calico-node、calico-kube-controllers 变为Running
# 然后检查节点状态，节点会从 NotReady -> Ready
kubectl get nodes
```


Operator 的`spec.registry`直接指向你的私服地址：

```
kubectl patch installation default -p '{"spec":{"registry":"10.150.12.157:8084"}}' --type=merge
```

校验：

```
kubectl get installation default -o yaml | grep registry
```

## Worker 节点加入集群（所有 worker 执行）

直接复制 master 输出的`kubeadm join`命令，**worker 节点用 sudo 执行**

```bash
sudo kubeadm join 10.150.12.160:6443 --token xz4m2h.j1wp3f95f07d96u3 \
        --discovery-token-ca-cert-hash sha256:4f5c20e00e1c21e55aa10654fead6d638c141b656e038a9ae4250ff795ac3a2e    
```


## 集群验证（master 执行）

```bash
#查看节点状态，全部Ready才算成功
kubectl get nodes

#查看集群组件状态
kubectl get cs

#跑测试pod验证
kubectl create deployment nginx --image=nginx
kubectl get pods
```

## 通过GitLab CICD部署k8s应用

在GitLab Runner所在服务器上安装 kubectl，并存在`~/.kube/config`，并设置权限：


```shell
sudo mkdir -p /home/gitlab-runner/.kube
sudo cp ~/.kube/config /home/gitlab-runner/.kube/config
sudo chown -R gitlab-runner:gitlab-runner /home/gitlab-runner/.kube
```


可以测试执行下部署文件：

```
kubectl apply -f api-gateway-service-deployment.yaml --dry-run=client
```


在`.gitlab-ci.yml`中添加如下内容：

```yml
variables:
  # 部署环境，小写，可选dev、sit、uat、prod等
  DEPLOYMENT_ENV: "dev"

## ========== k8s部署（流水线计划调度触发） ==========
k8s-deploy:
  stage: k8s-deploy
  rules:
    # 只有计划调度流水线才执行本job
    - if: $CI_PIPELINE_SOURCE == "schedule"
    # 其他所有触发来源：跳过该job
    - when: never
  script:
    # k8s部署
    # GitLab 会自动将 File 类型变量的内容写入临时文件，并将路径赋值给变量名
    - export KUBECONFIG=${KUBECONFIG_FILE}
    # 验证连接
    - kubectl cluster-info
    # 部署
    - kubectl apply -f k8s-deployment-${DEPLOYMENT_ENV}.yaml --validate=false
```    

其中，KUBECONFIG_FILE作为 ‌File 类型变量‌（例如 KUBECONFIG）存入 GitLab 项目的 Settings > CI/CD > Variables 中。

DEPLOYMENT_ENV变量对应不同的环境。





## 参考引用


* <https://kubernetes.io/docs/home/>
