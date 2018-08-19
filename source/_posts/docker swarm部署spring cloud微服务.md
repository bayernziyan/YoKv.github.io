---
title: docker swarm部署spring cloud微服务
date: 2018-08-17 13:00:46
categories: 
- DevOps
---

# 项目
<!--more-->
## spring cloud项目
[项目github](https://github.com/YoKv/microservices-practise)
项目部署结构:
* app-a
* app-b
* eureka

最简化的spring cloud项目结构，注册中心eureka（非HA），两个应用分别注册，app-a通过定时任务和RestTemplate（负载均衡）调用app-b服务。

## 制作镜像
每个应用都有对应的Dockerfile，在travis.com自动发布镜像，基础镜像是 `openjdk:jre-slim` ，jre版本是10.0.2。Dockerfile样例：
```
FROM openjdk:jre-slim

MAINTAINER yokv ez4tzl@hotmail.com

ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

ADD ./target/app.jar  app.jar

CMD ["/bin/sh", "-c", "java -jar app.jar"]
```

**注意**：不同的系统在定义系统环境变量时可能不同，比如slim是使用alpha镜像只能通过`SPRING_PROFILES_ACTIVE`来定义，而比如基于`centos`的`java:8`镜像可以直接使用与`application.yml`相同的变量名。

项目代码与镜像构建完成后就是部署啦。

# 原生swarm
原生swarm安装非常简单，安装完`docker-ce`后，只需要使用`docker swarm init`命令就能初始化一个集群。
为了确保所有应用能通信，需要应用在同一个子网段
创建一个overlay网络

```
docker network create -d overlay --subnet 10.0.1.0/24 cloud-net
```
同时要在项目中制定优先使用的网段：
```
spring.cloud.inetutils.preferred-networks=10.0.1.
```
在master节点就能部署应用了,命令：

```
docker stack deploy app -c docker-stack.yml
```

***docker-stack.yml***

```
version: "3"
services:
  eureka:
    image: yokv/sc-devops-eureka
    ports:
    - 8761:8761
    networks:
    - cloud-net
    environment:
      SPRING_PROFILES_ACTIVE: stack
    deploy:
      restart_policy:
        condition: on-failure
      resources:
        limits:
          cpus: '2'
          memory: 2048M
        reservations:
          cpus: '0.50'
          memory: 512M

  app-a:
    image: yokv/sc-devops-app-a
    networks:
    - cloud-net
    environment:
      SPRING_PROFILES_ACTIVE: stack
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
      resources:
        limits:
          cpus: '2'
          memory: 2048M
        reservations:
          cpus: '0.50'
          memory: 512M

  app-b:
    image: yokv/sc-devops-app-b
    networks:
    - cloud-net
    environment:
      SPRING_PROFILES_ACTIVE: stack
    deploy:
      replicas: 3
      restart_policy:
        condition: on-failure
      resources:
        limits:
          cpus: '2'
          memory: 2048M
        reservations:
          cpus: '0.50'
          memory: 512M

networks:
  cloud-net:
    external:
      name: cloud-net
```



