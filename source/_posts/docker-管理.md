---
title: docker-管理
date: 2018-05-29 23:45:48
categories: 
- DevOps
---

## docker私有仓库
```
docker run -d -p 5000:5000 --restart=always --name registry -v /opt/docker-registry:/var/lib/registry registry:2
```
<!--more-->
## 仓库相关方案
Portus
Nexus docker
Harbor
## ui集群管理
Rancher Labs
各大云服务提供
红帽 fabric8
## 资料
[参考](http://dockone.io/article/2168)