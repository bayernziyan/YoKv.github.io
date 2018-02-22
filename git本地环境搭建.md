---
title: git本地环境搭建
date: 2018-02-22 17:30:27
categories:
- git
---

## 前言
本文介绍git本地环境搭建，在本地能都完成git的日常拉取和提交推送。

<!--more-->
## git安装(windows版)
[git官网下载](https://git-scm.com/)，下载完成后直接安装。
安装完成后可以通过git --version命令检验是否成功

### 配置
配置git的用户名和邮箱，用户名和邮箱与gitlab或github等git服务器的用户名和邮箱没有关联关系，但是推荐相同，便于识别。这个用户名和邮箱只在提交代码时标识代码提交者的信息（暂时只发现这个作用）。
```
 git config --global user.name "Your Name"
 git config --global user.email "email@example.com"
```
可以看到这里设置的是global 的作用域，若想要某个项目使用不一样的用户名和邮箱，可以项目单独设置，这里不做拓展。

### 认证
在git中需要账户认证才能进行操作，有两种方式。在项目主页可以看到一个下拉选择有两个选项：SSH，HTTP
#### http
http认证不需要特别配置，克隆项目时选择HTTP，复制```http://xxxxxx.git```，在本地clone
#### ssh
ssh需要进行配置
1. 本地生成ssh 公钥和私钥
2. 将公钥复制到gitlab，gitlab上配置在 个人资料设置 》 SSH密钥 》添加密钥

本地生成SSH公钥和私钥参考[官方](https://git-scm.com/book/zh/v1/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E7%94%9F%E6%88%90-SSH-%E5%85%AC%E9%92%A5)，gitlab介绍也有教程，在个人资料设置 》 SSH密钥 页面中找到``` 生成密钥```的链接。

配置完成后，复制```git@xxxxxx.git ```，在本地clone

## git含界面的客户端
git很多带界面的客户端，[git含界面的客户端](https://git-scm.com/downloads/guis)。推荐SourceTree，windows,mac都有。
同时idea,eclipse有git支持。
接下来介绍这两个客户端使用(介绍clone)。
### idea
成功安装了git后，可以直接使用idea的版本控制，步骤：
1. 工具栏VCS 》 checkout from version control 》 git 就会出现一个弹框，或者File 》 new 》 project from version control 》 git
2. git repository url 指项目主页上的url git@xxxxxx.git 或 http://xxxxxx.git; parent directory 指项目本地存放路径;directory name 指项目名
3. 填写完后，点击clone,就能将git服务器上项目克隆到本地

### SourceTree
下载安装[SourceTree](https://www.sourcetreeapp.com/)
[SourceTree安装教程博客推荐](https://www.cnblogs.com/Lam7/p/6004737.html)

在文件 》 克隆 菜单下克隆
url 指项目主页上的url git@xxxxxx.git 或 http://xxxxxx.git

## git 命令行
也可以不使用含界面的客户端，直接使用命令行，只需要安装git
[详细命令清单](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)

## git 常用功能
### clone
克隆代码
### commit
本地修改的代码提交到本地仓库
### push
本地仓库的改动推送到远程仓库
### pull
远程查看的改动同步到本地


