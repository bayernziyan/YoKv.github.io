---
title: golang开发环境搭建
date: 2017-12-15 23:51:47
categories:
- golang基础
---

## 下载
[官网](https://golang.org)
本文选择zip格式，下载解压

## 环境变量
添加环境变量
GOLANG_HOME:C:\Program Files\go
加入到path变量
%GOLANG_HOME%\bin

## IDE
选择Jetbrains的Goland

<!--more-->

## demo 
[参考博客](http://www.jianshu.com/p/eb35a47a157e)

### 项目工程结构
go语言没有类似maven,ant的管理工具，项目目录下有3个子目录：

├── bin
├── pkg
└── src
* bin文件夹存放go install命名生成的可执行文件，可以把$GOPATH/bin路径加入到PATH环境变量里，就和我们上面配置的$GOROOT/bin一样，这样就可以直接在终端里使用我们go开发生成的程序了。
* pkg文件夹是存在go编译生成的文件。
* src存放的是我们的go源代码，不同工程项目的代码以包名区分。

IDE设置GOPATH,新建一个go文件
```
package main

import "fmt"

func main() {
	fmt.Println("something")
}

```
package 是一个关键字，定义一个包，和Java里的package一样，也是模块化的关键。
main包是一个特殊的包名，它表示当前是一个可执行程序，而不是一个库。

点击run可以看到控制台输出,第一个程序就完成了。