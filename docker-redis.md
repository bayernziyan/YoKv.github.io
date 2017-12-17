---
title: docker-redis
date: 2017-11-25 21:45:48
categories: 
- docker
---

## 下载镜像
docker pull redis

## 启动命令
docker run -d -p 6379:6379 -v /VOLUME:/data --name redis {imageID} redis-server  --requirepass {password}
 
修改相应的imageID 和 password 即可启动一个redis实例

[参考](https://github.com/dockerfile/redis)