---
title: docker构建基础件命令记录
date: 2018-03-04 17:30:27
categories:
- DevOps
---

## 说明
一些需要自定义Dockerfile的镜像在github上自动构建，本文记录一些现成的镜像使用，便于下次直接使用。
<!--more-->

## 不同容器数据挂载
### 数据卷容器
随便选择一个存在的镜像，使用-v
docker run -d -v D:/docker/volumes:/data --name dataV   java

### 应用容器
Java镜像的spring boot应用，jar包为demo.jar

docker run -d --volumes-from dataV  -p 5678:5678 --name sprinbgbootdemo java java -jar /data/demo.jar


## mysql

```
docker run -p 1006:3306 -v  /docker/volumes/mysql:/var/lib/mysql --name mysql-server -e MYSQL_ROOT_PASSWORD=mysql1122 -d mysql:5.7.20

```

## mongo 部署

### 镜像
```
docker pull mongo:3.6

```

### 一个过渡使用的容器，用于生成账号，数据库等配置文件

#### 启动容器
```
docker run -d -v  /docker/volumes/mongo:/data/db  --name mongoTransient mongo:3.6
```
#### 进入容器
```
docker exec -it mongoTransient mongo 
```
#### 设置

```
use admin
db.createUser({ user: 'admin23', pwd: 'mongo135', roles: [ { role: "userAdminAnyDatabase", db: "admin" }] });

use dota
db.createUser({ user: 'mongouser', pwd: 'mongo135', roles: [ { role: "readWrite", db: "dota" }] });

db.auth("mongouser","mongo135")

use admin
db.system.users.find()
```


###	实际使用的容器

```
docker run -d -p 27017:27017 -v  /docker/volumes/mongo:/data/db  --name mongoApp mongo:3.6 --auth
```


## postgresql
```
docker run -d  -e POSTGRES_PASSWORD=postgres534 -e POSTGRES_USER=postgresU -v /docker/volumes/postgresql:/var/lib/postgresql/data/pgdata -p 5400:5432  --name postgresApp postgres:10

docker run -d  -e POSTGRES_PASSWORD=postgres534 -e POSTGRES_USER=postgresU -p 5400:5432  --name postgresApp postgres:10

docker run -it  -e POSTGRES_PASSWORD=postgres534 -e POSTGRES_USER=postgresU -v /docker/volumes/postgresql:/var/lib/postgresql/data/pgdata -p 5400:5432  --name postgresApp postgres:10

```

jdbc:postgresql://aiyo.space:5400/postgres


## redis
```

docker run -d -p 6379:6379 -v /VOLUME:/data --name redis 8f2e redis-server  --requirepass redis112

docker run -d -p 6300:6379 --name redis-aiyo redis  --requirepass redis112

```