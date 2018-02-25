---
title: Github Pages博客实践
date: 2017-12-18 17:30:27
categories:
- 项目实践
---


## 介绍
本文介绍搭建本套博客系统的过程。博客原始文档git push后能直接发布修改。
<!--more-->

## git 项目
首先在GitHub新建一个repository，名字以用户名加github.io，例如我的仓库YoKv.github.io。这个项目就能被github识别为Github Pages。在域名管理页面使用CNAME方式添加blog.aiyo.space域名指向https://YoKv.github.io,这样就可以通过自己域名访问自己的博客。项目

## hexo docker镜像
Github Pages识别的是静态网站项目，本博客通过hexo项目生成静态网站文件，并且采用next主题。
使用Dockerfile定制hexo镜像：
```
FROM node:slim
MAINTAINER yokv "ez4tzl@hotmail.com"

# install hexo
 RUN npm install hexo-cli -g

# hexo init
 RUN mkdir hexo \
 && cd hexo \
 && hexo init blog 

# download next 
RUN wget -c https://codeload.github.com/iissnan/hexo-theme-next/tar.gz/v5.1.1 -O - |  tar -xz -C /hexo/blog/themes/ \
&& mv /hexo/blog/themes/hexo-theme-next-5.1.1 /hexo/blog/themes/next
 
# config
RUN wget -P /hexo/blog http://p0vlap8z6.bkt.clouddn.com/hexo_config.yml \
&& mv /hexo/blog/hexo_config.yml  /hexo/blog/_config.yml \
&& wget -P /hexo/blog/themes/next http://p0vlap8z6.bkt.clouddn.com/themesnext_config.yml \
&& mv /hexo/blog/themes/next/themesnext_config.yml   /hexo/blog/themes/next/_config.yml 

#application's port
EXPOSE 4000

WORKDIR /hexo/blog
# run hexo server
CMD ["hexo","server"]
```

其中hexo的配置文件和主题的配置文件从七牛云下载(已删除)，便于保存和使用。
通过以下命令就能够生成静态网站页面:
```
docker run -d -v /docker/volumes/blog/source:/hexo/blog/source -v /docker/volumes/blog/public:/hexo/blog/public -p 6700:4000 --name hexo-server hexo:app sh -c 'hexo g && hexo s'
```

在挂载的public目录下就是生成的静态网站资源文件。docker run 命令也可以只使用hexo g，使用docker s启动了服务可以通过aiyo.space:6700访问到，做了反向代理后可通过b.aiyo.space访问备用,毕竟github国外的问题有可能访问速度慢。将public下的文件push到什么新建的repository，blog.aiyo.space就能看到博客部署完成。

## webhook 实现自动部署
为方便使用，使用github的webhook功能实现自动部署，实现push了原始的markdown文件，其余的事情交给服务器处理，原本需要的操作是：本地编写markdown->push到github->服务器pull->hexo g->服务器push资源文件。

编写部署脚本blogDeploy.sh:

```
#!/bin/bash
cd /docker/volumes/blog/source/_posts
git pull origin
docker restart 1c
sleep 30
cd /docker/volumes/blog/public
git add -A
git commit -m "blog update"
git push origin master
```

webhook服务器：
见webHook项目

github配置webhook服务器链接,serect。
