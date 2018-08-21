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
    environment:
      - DB_ADAPTER=postgresql
      - DB_HOST=postgresql
      - DB_PORT=5432
      - DB_USER=postgres
      - DB_PASS=f4ef54b039dc6794
      - DB_NAME=db
      - GITLAB_TIMEZONE=Asia/Shanghai
      - TZ=Asia/Shanghai
    ports:
      - 80:80
    volumes:
      - '/srv/gitlab/config:/etc/gitlab'
      - '/srv/gitlab/logs:/var/log/gitlab'
      - '/srv/gitlab/data:/var/opt/gitlab'
    networks:
      - gitlab-net
    
  gitlab-runner:
    image: gitlab/gitlab-runner
    container_name: gitlab-runner
    restart: always
    volumes:
      - '/srv/gitlab-runner:/etc/gitlab-runner'
      - '/var/run/docker.sock:/var/run/docker.sock'
    networks:
      - gitlab-net
      
  sonarqube:
    image: sonarqube
    restart: always
    environment:
      - SONARQUBE_JDBC_USERNAME=postgres
      - SONARQUBE_JDBC_PASSWORD=f4ef54b039dc6794
      - SONARQUBE_JDBC_URL=jdbc:postgresql://postgres:5432/db
    ports:
      - 9000:9000
      - 9092:9092
    volumes:
      - 'sonarqube:/opt/sonarqube/conf'
      - 'sonarqube:/opt/sonarqube/data'
      - 'sonarqube:/opt/sonarqube/extensions'
      - 'sonarqube:/opt/sonarqube/lib/bundled-plugins'
    networks:
      - gitlab-net
    
  postgres:
    image: postgres
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=f4ef54b039dc6794
      - POSTGRES_DB=db
    volumes:
      - 'postgres:/var/lib/postgresql/data'
    networks:
      - gitlab-net
      
networks:
  gitlab-net:
  
volumes:
  sonarqube:
  postgres:
```


# 配置

##  gitlab runner

```
docker exec -it gitlab-runner  gitlab-runner register -n \
  --url http://localhost/ \
  --registration-token xxx \
  --tag-list=dind \
  --description "dind" \
  --docker-privileged=false \
  --docker-pull-policy="if-not-present" \
  --docker-image "docker" \
  --docker-volumes /var/run/docker.sock:/var/run/docker.sock \
  --docker-volumes /root/.m2/:/root/.m2/ \
  --executor docker
```

# 使用

