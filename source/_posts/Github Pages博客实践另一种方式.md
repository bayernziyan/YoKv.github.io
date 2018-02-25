---
title: Github Pages博客实践另一种方式
date: 2018-02-25 17:30:27
categories:
- 项目实践
---


## 介绍
上篇文章讲述的博客部署方式有些繁琐，本文使用github+travis持续部署Github Pages
<!--more-->
[参考](https://www.cnblogs.com/dmego/p/7664877.html)
## git 项目
首先在GitHub新建一个repository，名字以用户名加github.io，例如我的仓库YoKv.github.io。这个项目就能被github识别为Github Pages。
### 域名
在域名管理页面使用CNAME方式添加blog.aiyo.space域名指向https://YoKv.github.io,这样就可以通过自己域名访问自己的博客项目

### 分支
为了方便，将master作为Github Pages，代码主要是html代码（静态页面）
另起一个分支 sourceFile,作为原始资源，travis持续部署在这个分支上生效，并且最后把静态页面代码提交到master分支上

## sourceFile分支
使用hexo时，会有一个node.js项目作为博客项目，而sourceFile就是这个node.js项目。
[Hexo文档](https://hexo.io/zh-cn/docs/setup.html)

如图：
![](/images/hexopj.jpg)

```
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes

```
* _config.yml
	网站的 配置 信息，您可以在此配置大部分的参数。
* package.json
	应用程序的信息。EJS, Stylus 和 Markdown renderer 已默认安装
* scaffolds
	模版 文件夹。当您新建文章时，Hexo 会根据 scaffold 来建立文件。
	Hexo的模板是指在新建的markdown文件中默认填充的内容。例如，如果您修改scaffold/post.md中的Front-matter内容，那么每次新建一篇文章时都会包含这个修改。
* source
	资源文件夹是存放用户资源的地方。除 _posts 文件夹之外，开头命名为 _ (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 public 文件夹，而其他文件会被拷贝过去。
* themes
	主题 文件夹。Hexo 会根据主题来生成静态页面。
## .travis.yml
travis 配置文件，包含了集成的环境，脚本

```yml
language: node_js
node_js: stable

install:
  - npm install

script:
  - hexo g

after_script:
  - cd ./public
  - git init
  - git config user.name "YoKv"
  - git config user.email "ez4tzl@hotmail.com"
  - git add .
  - git commit -m "update blog by travis"
  - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" master:master

branches:
  only:
    - sourceFile 
env:
 global:
   - GH_REF: github.com/YoKv/YoKv.github.io.git
```

可以看到首先是node.js环境，将sourceFile分支依赖安装好，然后通过脚本```hexo g```生成静态网站文件，最后通过git推送到master分支
其中，git推送采用的是token的方式，不是https或ssh

## 结果
[github项目](https://github.com/YoKv/YoKv.github.io)
集成结果
![](/images/travisexample.jpg)
