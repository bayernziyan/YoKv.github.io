---
title: docker-概述
date: 2018-04-29 15:45:48
categories: 
- DevOps
---


# 容器引擎技术
## 概览
### 操作系统：
* CoreOS

### 集群管理
* kubernetes(k8s)
* Swarm
* Mesos

### 容器技术
* Docker
* rkt


附上[容器引擎大战历史](https://www.kubernetes.org.cn/2250.html)

## Docker介绍
Docker是Docker公司开源的一个基于轻量级虚拟化技术的容器引擎项目,整个项目基于 Go语言开发，并遵从Apache 2.0协议。
Docker 的核心理念是 Build, Ship, and Run Any App, Anywhere。

历经5年发展，Docker公司揭露了今年最新的Docker年度数据报告，从2013年3月PyCon大会上，Docker首度亮相之后，至今在Docker上的容器镜像下载次数已经超过了370亿次，容器化的应用有高达350万个，目前在LinkedIn网站上的Docker相关职缺也有15,000个。全球活跃的Docker使用者社群已有200多个，包括台湾也有。全球使用企业版Docker EE的企业顾客目前则约有450家。
[链接](https://www.kubernetes.org.cn/3816.html)


## 为什么使用docker

docker解决了什么问题:

* 程序在我这运行得好好的，在你那怎么就不行了？
* 系统好卡，哪个进程把cpu给吃光了？
* 这套系统我已经搭建好了，要不你再搭建一遍？

docker意义:

* 加速dovops普及
* 变革现有paas平台
* 容器即服务（caas）


# docker 基础概念

## 核心组件

* Docker
	* Docker Enterprise Edition
	* Docker Community Edition
		* Docker
		* Docker for Mac
		* Docker for Windows
* Docker Store
* Docker Hub
* Open-source projects
	* Docker Notary
	* Docker Registry
* Docker Machine
* Docker Compose
* Docker Cloud






# docker编排
 
## Docker Swarm集群管理
