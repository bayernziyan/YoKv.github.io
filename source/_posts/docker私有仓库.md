---
title: docker私有仓库
date: 2018-05-29 23:45:48
categories: 
- DevOps
---

# docker私服
尝试多个热门的私服工具
<!--more-->
## Nexus

```
docker run -d -p 8081:8081 -p 5000:5000 --name nexus sonatype/nexus3
```
在nexus管理界面新建docker仓库（hosted），端口填5000，会生成一个doker-registry私服

## Portus
## Shipyard

## 官方
[openssl制作的证书](https://docs.docker.com/registry/insecure/#use-self-signed-certificates)
[docs](https://docs.docker.com/registry/deploying/)

```
$ mkdir -p certs

$ openssl req \
  -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key \
  -x509 -days 365 -out certs/domain.crt
```

将证书复制到其他docker主机上
```
Linux: Copy the domain.crt file to /etc/docker/certs.d/myregistrydomain.com/ca.crt on every Docker host. You do not need to restart Docker.
```

```
docker run -d \
  --restart=always \
  --name registry \
  -v /opt/docker-registry:/var/lib/registry \
  -v /root/certs:/certs \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -p 443:443 \
  registry:2

```
## 官方方案二
```
openssl req -newkey rsa:2048 -nodes -sha256 -keyout /root/certs/domain.key -x509 -days 365 -out /root/certs/domain.crt
```
```
 docker run -d \
  --restart=always \
  --name registry \
  -v /opt/docker-registry:/var/lib/registry \
  -v /root/certs:/certs \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -p 443:443 \
  registry:2

```
客户机上
```
openssl s_client -connect registry.vpcflow.com:443 -showcerts </dev/null 2>/dev/null | openssl x509 -outform PEM | tee /etc/pki/ca-trust/source/anchors/registry.vpcflow.com.crt

update-ca-trust

/bin/systemctl restart docker.service

```
## 管理界面
portainer/portainer
konradkleine/docker-registry-frontend:v2

## harbor
[官网](https://github.com/vmware/harbor)
1. 下载installer
2. 配置harbor.cfg
3. 执行install.sh

