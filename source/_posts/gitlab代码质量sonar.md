---
title:  gitlab代码质量sonar
date: 2018-08-19 21:00:46
categories: 
- DevOps
---

# 安装
<!--more-->

`docker-compose.yml`:

```
version: "3"
services:
  gitlab:
    image: gitlab/gitlab-ce
    restart: always
    hostname: localhost
#    environment:
#      GITLAB_OMNIBUS_CONFIG: |
#        external_url http://localhost:8080
#        gitlab_rails['gitlab_shell_ssh_port'] = 2224
    ports:
    - 80:80
    - 222:22
    networks:
    - gitlab-net
   
  sonarqube:
    image: sonarqube
    restart: always
    ports:
    - 9000:9000
    - 9092:9092
    networks:
    - gitlab-net

networks:
  gitlab-net:

# 外部共用db

```

# 配置


# 使用

