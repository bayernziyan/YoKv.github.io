---
title: docker概念与安装
date: 2017-07-23 19:42:13
categories: 
- docker
- DevOps
---


## 概念
<!--more-->
### 镜像

>  类似于虚拟机镜像，是一个只读的模板。一个镜像可以包含一个基本的操作系统，里面仅安装一个Apache应用程序。可以叫做Apache镜像。

### 容器
>  容器类似于一个轻量级的沙箱，docker利用容器，来运行和隔离应用。容器是从镜像创建的应用程序实例。可以将容器看作一个简易版的linux系统环境以及运行在其中的应用程序打包而成的盒子。

### 仓库
>  仓库类似于代码仓库，是docker集中存放镜像文件的场所。仓库分为公开仓库和私有仓库，最大的公开仓库是Docker Hub。仓库管理与Git非常相似。

#### 仓库和仓库注册服务器区分
>  仓库注册服务器是存放仓库的地方，往往有多个仓库，每个仓库集中存放某一类镜像，往往包括多个镜像文件，通过tag区分。

## 安装
>  本文以CentOS 7为例。
>  官网有Docker Platform,Docker Hub,Docker Cloud和Docker DataCenter。

* Docker Platform 适合桌面系统或云平台
* Docker Hub 官方提供的云托管服务，提供镜像仓库
* Docker Cloud 官方提供的容器云服务，可以完成容器的部署和管理，可以完整地支持容器化项目，有CI，CD功能
* Docker DataCenter  企业级的简单安全弹性容器集群编排和管理

#### 开始安装

>  CentOS-Extras 内置了Docker,直接通过命令yum命令安装：
>  yum install docker
>  或yum install -y docker-engine

通过shell脚本安装
```
	curl -fsSL https://get.docker.com/ | sh
或
	wget -qO https://get.docker.com/ | sh
```

####  配置服务
>  避免使用docker命令都要用特殊身份，将当前用户加入安装中自动创建的docker用户组：

```
usermod -aG docker username
```

默认配置文件/etc/default/docker

管理服务
```
systemctl start docker.service
```

日志
```
tail /var/log/upstart/docker.log
```