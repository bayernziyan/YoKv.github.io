---
title: docker-ce安装
date: 2017-11-24 21:45:48
categories: 
- docker
---

## 安装docker-ce
Docker有两种安装方式，分为存储库和包安装;官方指南推荐存储库安装Docker，方便安装和升级任务；本文以存储库的方式安装Docker，存储库方式安装的版本相对较落后，是稳定版本。通过官方的shell脚本安装(curl)为最新版本，例如17年11月版本为17.11，而存储库的版本是17.09，是稳定版本。
<!--more-->

安装  yum-utils ,它可以提供 yum-config-manager 来管理repository及扩展包
```
$ sudo yum install -y yum-utils

```

添加库
```
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

更新 yum 包
```
$ sudo yum makecache fast
```

安装Docker CE

```
$ sudo yum install docker-ce
```

启动Docker CE
```
$ sudo systemctl start docker
```

## 阿里云私有镜像
http://cr.console.aliyun.com
1.创建名称空间
2.创建镜像仓库

和git一样操作镜像
```
$ docker pull registry.cn-hangzhou.aliyuncs.com/mykernel/test1:[镜像版本号]
```

```
  $ docker login --username=xxx registry.cn-hangzhou.aliyuncs.com
  $ docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/mykernel/test1:[镜像版本号]
  $ docker push registry.cn-hangzhou.aliyuncs.com/mykernel/test1:[镜像版本号]
```
从内网push镜像，速度将大大提升，并且将不会损耗公网流量。机器是在vpc网络的，使用registry-cn-hangzhou-vpc.aliyuncs.com的域名前缀进行推送。