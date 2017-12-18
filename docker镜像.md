---
title: docker镜像
date: 2017-07-24 21:45:48
categories: 
- docker
- DevOps
---

## 镜像操作
<!--more-->
#### 获取镜像
```
docker pull NAME[:TAG]
```
#### 创建一个容器
```
docker run -it NAME[:TAG] bash
```
#### 列出镜像
```
docker images
```
镜像id 使用时不需要输入完整
#### 镜像打标签
```
docker tag ubuntu:latest myimage:tag
```
image id 一样,显示两个镜像
#### 镜像详细信息
```
docker inspect NAME[:TAG]
```
#### 镜像历史
```
docker history
```
#### 镜像搜索
默认搜索官方共享镜像
```
docker search TERM
docker --automated true --no-trunc true -s 0 nginx
```
#### 删除镜像,删除容器
```
docker rmi myimage:tag
docker rmi a625sd...

docker rm a21fsd...
```
#### 查看容器
```
docker ps -a
```

#### 创建镜像
```
基于已有镜像的容器
docker commit [OPTIONS] CONTAINER [repo[:tag]]

直接导入
cat ubuntu xxxx.tar.gz | docker import - ubuntu:14.04

```

```
存出镜像
docker save -o ubuntu.tar ubuntu:14.04
载入镜像
docker load --input ubuntu.tar
或
docker load < ubuntu.tar
```
#### 上传镜像
```
docker push NAME[:TAG]
```