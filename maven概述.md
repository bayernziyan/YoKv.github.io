---
title: maven概述
date: 2017-11-02 17:30:27
categories:
- maven
---

## 安装
* Java环境
* 下载解压Maven
* 环境变量
<!--more-->

## 全局配置settings.xml
单独文章分解

## 本地库
默认的 .m2 目录下，可在settings.xml修改

## Maven 私服
nexus

## Pom
POM 包含的项目是使用 Maven 来构建的，它用来包含各种配置信息。
POM 也包含了目标和插件。在执行任务或目标时，Maven 会使用当前目录中的 POM。它读取POM得到所需要的配置信息，然后执行目标。部分的配置可以在 POM 使用如下：
* project dependencies
* plugins
* goals
* build profiles
* project version
* developers
* mailing list
创建一个POM之前，应该要先决定项目组(groupId)，它的名字(artifactId)和版本，因为这些属性在项目仓库是唯一标识的。

## Maven 生命周期

|阶段|	处理|	描述|
|----：|----：|----：|
|准备资源	|资源复制	|资源复制可以进行定制|
|编译	|执行编译	|源代码编译在此阶段完成|
|包装|	打包	|创建JAR/WAR包如在 pom.xml 中定义提及的包|
|安装	|安装	|这一阶段在本地/远程Maven仓库安装程序包|

## 自动化部署，持续集成


[相关资源](http://blog.csdn.net/u012152619/article/category/6239920)