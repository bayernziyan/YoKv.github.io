---
title: gitlab持续集成
date: 2018-08-13 13:00:46
categories: 
- DevOps
---

# gitlab持续集成

## 介绍
通过.gitlab-ci.yml来定义持续集成方式
通过定义runner来执行集成环境
最终产出的是镜像
## 安装runner
<!--more-->
### 方式一(未采用)
先将runner注册到girlab,配置数据保留在宿主机 /srv/gitlab-runner/config
```
  docker run --rm -t -i -v /srv/gitlab-runner/config:/etc/gitlab-runner --name gitlab-runner-register  gitlab/gitlab-runner:v10.6.1 register
```
然后启动gitlab-runner把配置文件挂载到相应目录
```
docker run -d --name gitlab-runner --restart always -v /srv/gitlab-runner/config:/etc/gitlab-runner -v /var/run/docker.sock:/var/run/docker.sock gitlab/gitlab-runner:v10.6.1
```
### 方式二
[gitlab runner生成镜像](https://docs.gitlab.com/ce/ci/docker/using_docker_build.html)
生成镜像gitlab runner提供了3种方式，选择了第3种
```
docker run -d --name gitlab-runner --restart always -v /srv/gitlab-runner/config:/etc/gitlab-runner -v /var/run/docker.sock:/var/run/docker.sock gitlab/gitlab-runner:v10.6.1
```
进入容器执行注册，自定义程度比方式一高，方式一只能按官方的提示填
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
**由于要生成镜像，需要挂载docker.sock**


## .gitlab-ci.yml配置
[doc](https://docs.gitlab.com/ce/ci/yaml/)
```
variables:
  DOCKER_DRIVER: overlay2
  MAVEN_CLI_OPTS: -s /root/.m2/settings.xml --batch-mode
  MAVEN_OPTS: -Dmaven.test.skip=true -Dmaven.repo.local=/root/.m2/repository

stages:
  - dependencies
  - build
  - package

maven-build:
  image: maven:3-jdk-8
  stage: build
  script:
    - mvn clean package $MAVEN_CLI_OPTS
  cache:
    paths:
      - /root/.m2/repository/
  artifacts:
    paths:
      - target/*.jar
  # 构建的分支
  only:
    refs:
      - master

docker-build:
  image: docker:latest
  stage: package
  script:
  - docker build -t yokv/xxx .
  # 构建的分支
  only:
    refs:
      - docker
```