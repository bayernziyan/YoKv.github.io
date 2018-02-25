---
title: go语言demo
date: 2017-12-16 12:00:47
categories:
- golang基础
---

## demo介绍
[fork的源代码](https://github.com/YoKv/code/tree/master/chapter2/sample)
程序从不同的数据源拉取数据，将数据项与搜索项对比，将匹配的内容显示再终端窗口。
<!--more-->

## 部分语法
引用类型要使用make构造。

### 简化变量运算符 :=
这个运算符用于声明一个变量，同时赋予初值。
与var相比，var用于声明零值的变量。

### 启动goroutine

```
	for _, feed := range feeds {
		// Retrieve a matcher for the search.
		matcher, exists := matchers[feed.Type]
		if !exists {
			matcher = matchers["default"]
		}

		// Launch the goroutine to perform the search.
		go func(matcher Matcher, feed *Feed) {
			Match(matcher, feed, searchTerm, results)
			waitGroup.Done()
		}(matcher, feed)
	}
```

for range是个迭代的关键字

接口与实现