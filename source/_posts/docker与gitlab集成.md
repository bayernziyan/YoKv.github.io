---
title: docker与gitlab集成
date: 2018-05-29 23:45:48
categories: 
- DevOps
---

# docker私服
尝试多个热门的私服工具
<!--more-->
## Nexus

```
docker run -d -p 8081:8081 -p 5000:5000 --name nexus -v /data/nexus-data:/nexus-data sonatype/nexus3
```
在nexus管理界面新建docker仓库（hosted），端口填5000，会生成一个doker-registry私服

## Portus
太麻烦，略过
## Shipyard

## 官方
```
docker run -d -p 5000:5000 --restart=always --name registry -v /opt/docker-registry:/var/lib/registry registry:2
```

[docs](https://docs.docker.com/registry/deploying/)


## 界面
portainer/portainer
暂时没有registry好的仓库
konradkleine/docker-registry-frontend:v2

## 最后选择 
阿里云的容器镜像服务，免费的。。。

# gitlab
[docs](https://docs.gitlab.com/omnibus/docker/)
```
 docker run --detach --hostname localhost --publish 80:80 --publish 5005:5005  --name gitlab gitlab/gitlab-ce:latest
```