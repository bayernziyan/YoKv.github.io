---
title: k8s安装
date: 2018-07-07 23:45:48
categories: 
- DevOps
---

# k8s安装

<!--more-->
## 使用kubeadm安装k8s集群
centos7版本  1804
k8s版本 v1.11.1
docker版本17.03

### yum安装docker

```
   sudo yum install -y yum-utils \
    device-mapper-persistent-data \
    lvm2
  
   sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
    
   sudo yum install docker-ce
   sudo systemctl enable docker
   sudo systemctl start docker
```
  
  网络连不到docker官方源，可以下载[rpm离线安装](https://download.docker.com/linux/centos/7/x86_64/stable/Packages/)
  
  设置国内镜像加速
  
  ### 准备k8s需要的docker镜像
  镜像仓库在 k8s.gcr.io，访问不到，使用docker hub中转
  [k8s镜像中转项目](https://github.com/YoKv/k8s-auto-imges)
```
  docker pull yokv/kube-apiserver-amd64:v1.11.1
  docker pull yokv/kube-controller-manager-amd64:v1.11.1
  docker pull yokv/kube-scheduler-amd64:v1.11.1
  docker pull yokv/kube-proxy-amd64:v1.11.1
  docker pull yokv/pause:3.1
  docker pull yokv/etcd-amd64:3.2.18
  docker pull yokv/coredns:1.1.3

  docker tag yokv/kube-apiserver-amd64:v1.11.1 k8s.gcr.io/kube-apiserver-amd64:v1.11.1
  docker tag yokv/kube-controller-manager-amd64:v1.11.1 k8s.gcr.io/kube-controller-manager-amd64:v1.11.1
  docker tag yokv/kube-scheduler-amd64:v1.11.1 k8s.gcr.io/kube-scheduler-amd64:v1.11.1
  docker tag yokv/kube-proxy-amd64:v1.11.1 k8s.gcr.io/kube-proxy-amd64:v1.11.1
  docker tag yokv/pause:3.1 k8s.gcr.io/pause:3.1
  docker tag yokv/etcd-amd64:3.2.18 k8s.gcr.io/etcd-amd64:3.2.18
  docker tag yokv/coredns:1.1.3 k8s.gcr.io/coredns:1.1.3
```
  
  ### yum 安装
  [参考官方教程](https://kubernetes.io/docs/setup/independent/install-kubeadm/)

  由于访问不到官方源，使用rpm包离线安装
  
  在能访问到的机器上获取rpm包
```
  cat <<EOF > /etc/yum.repos.d/kubernetes.repo
  [kubernetes]
  name=Kubernetes
  baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
  enabled=1
  gpgcheck=1
  repo_gpgcheck=1
  gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
  exclude=kube* ###删了这行
  EOF  
  
  
  yum install yum-plugin-downloadonly -y
  yum install --downloadonly --downloaddir=./ kubelet kubeadm kubectl

  ```
  
  将rpm包拷贝到要安装的机器上
  ```
  yum install  kubelet kubeadm kubectl
  ```
  网络配置
  ```
  cat <<EOF >  /etc/sysctl.d/k8s.conf
  net.bridge.bridge-nf-call-ip6tables = 1
  net.bridge.bridge-nf-call-iptables = 1
  EOF

  sysctl --system
  systemctl stop firewalld
  systemctl disable firewalld
  ```
  
  ### 初始化集群master
  ```
  setenforce 0
  swapoff -a
  kubeadm init --kubernetes-version=v1.11.1
  systemctl enable kubelet && systemctl start kubelet
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

  kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/v0.10.0/Documentation/kube-flannel.yml
  ```
  
  可以看到
  ```
  
  Your Kubernetes master has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join 192.168.200.128:6443 --token biws01.pfgmitk3a72q0okx --discovery-token-ca-cert-hash sha256:16b863197d9a13ed07f10fd19a600e6063693ad60acc7e9ba2d7cc7d78c92656
  
  ```
  
  
  
  
  
  
  
