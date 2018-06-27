---
title: drone初探
date: 2018-06-27 13:00:46
categories: 
- DevOps
---

## gitlab安装 
<!--more-->
使用docker安装
``` 
 docker run -d --hostname localhost  -p 80:80 --name gitlab  --restart always  --volume /srv/gitlab/config:/etc/gitlab --volume /srv/gitlab/logs:/var/log/gitlab --volume /srv/gitlab/data:/var/opt/gitlab gitlab/gitlab-ce:latest 

```

## gitlab 配置 
在gitlabuser setting 或者 管理界面的applications 添加application 
**Redirect URI**为drone的地址 
	http://hostname:port/authorize

**重要配置**：在管理界面的settings的最下面有个**Outbound requests**设置，勾上`Allow requests to the local network from hooks and services`，意思就是识别本地的webhook地址，不勾选在以192.168的内网地址的drone的webhook会出现uri识别失败的情况，现象就是gitlab代码提交就不触发drone构建

## drone安装
使用docker compose安装

```
version: '2'
services:
  drone-server:
    image: drone/drone:0.8
    ports:
      - 8000:8000
      - 9000
    volumes:
      - /opt/drone:/var/lib/drone/
    restart: always
    environment:
	  - DRONE_OPEN= true
      - DRONE_ADMIN=admin
      - DRONE_HOST=http://192.168.10.123:8000
      - DRONE_GITLAB=true
      - DRONE_GITLAB_CLIENT=xxx
      - DRONE_GITLAB_SECRET=xxx
      - DRONE_GITLAB_URL=http://gitlab
      - DRONE_SECRET=gitlab
      - DRONE_GITLAB_SKIP_VERIFY=true
      - DRONE_GITLAB_PRIVATE_MODE=true

  drone-agent:
    image: drone/agent:0.8
    restart: always
    depends_on:
      - drone-server
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - DRONE_SERVER=drone-server:9000
      - DRONE_SECRET=gitlab

```