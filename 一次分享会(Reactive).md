---
title: 一次分享会（Reactive）
date: 2018-02-05 17:30:27
categories:
- 项目实践
---

# 手稿
目前仅后端开发领域，主流的开发语言有golang，python，php，ruby，还有基于JVM的kotlin，scala。高级编程语言都是不断的在进步中，比如java7到java8的更新，并且各个语言之间相互借鉴，
原本打算分享一下spring相关的技术，但是准备不够充分，还是下次再分享吧，本期分享的主题可能大家现在工作中可能用不到，但我相信在不远的将来，这个技术将会是主流。不管怎样，在现在后端语言快速发展的阶段，多了解一些比较前沿的技术还是有必要的。

写代码是件有趣的事，好奇心

# 主题——响应式编程（Reactive）
## 介绍
最近看到Dubbo 3.0将要在3月底发布，Spring Boot在经历了大半年的2.0里程碑版本（stands for milestone）迭代后，终于发布了RC版本（stands for Release Candidate），Release版本（正式版本）指日可待了，同时Spring Cloud的2.0版本也在稳步迭代。这些版本的迭代都包含了Reactive的内容。

* [Dubbo3.0变化：](https://mp.weixin.qq.com/s/PpH9xoj0FZAbjKHkA57AAw)
	Dubbo 3.0 将以 Streaming 为内核,重点是在服务治理和编程模型上,而编程模型的革新，其实就是使用了“反应式编程”模式（Reactive Programming）。在 Dubbo 3.0 中，reactive 将成为核心，会做到客户端、服务端、缓存和数据库，全程无阻塞。
* [Spring2.0变化：](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Release-Notes)
	1. Spring Framework 5.0
		Spring Boot 2.0 builds on and requires Spring Framework 5.0. There are a number of nice refinements in Spring Framework 5.0 including extensive support for **building reactive applications**. Please refer to the Spring Framework Wiki for details.
	2. WebFlux and WebFlux.fn support
		Spring Boot 2.0 provides a new starter for supporting the Reactive Spring web frameworks, for both annotation and functional based variants. spring-boot-starter-webflux brings WebFlux itself, plus Reactor Netty as a default web engine (spring-boot-starter-reactor-netty).
	3. Reactive data support
		Spring Boot 2.0 provides auto-configuration for the following data store with reactive support:
			* MongoDB (spring-boot-starter-data-mongodb-reactive)
			* Redis (spring-boot-starter-data-redis-reactive)
			* Cassandra (spring-boot-starter-data-cassandra-reactive)

	4. Reactive server customisation
		 Customizers for Jetty, Tomcat, and Undertow are now called when configuring a reactive web server.
	5. Reactive Couchbase support
		Support for Spring Data reactive repositories is available for Couchbase and a spring-boot-starter-data-couchbase-reactive is available to easily get started.
* java9:
	Reactive Streams

### 关键词
* **事件驱动**
* **发布订阅模型**
* **异步**
* **非阻塞**
* **stream**

### 概念 
响应式编程来源于数据流和变化的传播，意味着由底层的执行模型负责通过数据流来自动传播变化。这句话让我联想到vue.js的双向绑定，换句话说，vue.js来源于数据流和变化，可以不使用代码操作dom，自动传播数据变化，改变页面。另外举个例子，求值一个简单的表达式 c=a+b，当 a 或者 b 的值发生变化时，传统的编程范式需要对 a+b 进行重新计算来得到 c 的值。如果使用反应式编程，当 a 或者 b 的值发生变化时，c 的值会自动更新。

反应式流中第一个重要概念是负压/背压（backpressure）。在基本的消息推送模式中，当消息发布者产生数据的速度过快时，会使得消息订阅者的处理速度无法跟上产生的速度，从而给订阅者造成很大的压力。当压力过大时，有可能造成订阅者本身的奔溃，所产生的级联效应甚至可能造成整个系统的瘫痪。负压的作用在于提供一种从订阅者到生产者的反馈渠道。订阅者可以通过 request()方法来声明其一次所能处理的消息数量，而生产者就只会产生相应数量的消息，直到下一次 request()方法调用。这实际上变成了推拉结合的模式。







## 特点 
- 惰性计算 
- 函数是“第一等公民” 
- 只使用表达式而不使用语句 
- 没有副作用


## 缺点
* 难调试
* 事务控制

##
操作数据流，专注于逻辑，让你的代码更上一层楼。


# 理论与代码
## java原生实现

## 两大类库支持
### reactor(Spring5中响应式编程的基础)

### rxjava

## 主流框架
### spring5与 spring boot2.0

### vert.x


## 参考资料
[reactor官网](http://projectreactor.io/)
[rxjava官网](http://reactivex.io/)
[Wikipedia](https://en.wikipedia.org/wiki/Reactive_programming)
[Spring WebFlux Framework](https://docs.spring.io/spring-boot/docs/2.0.0.RC1/reference/htmlsingle/#boot-features-webflux)
[Vert.x 官网](http://vertx.io/)
[Vert.x 官方文档中文翻译](https://vertxchina.github.io/vertx-translation-chinese/)
[概念介绍gist](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)
[stackoverflow响应式编程讨论](https://stackoverflow.com/questions/1028250/what-is-functional-reactive-programming)
[其他优质分享(有视频)](https://academy.realm.io/cn/posts/droidcon-gomez-functional-reactive-programming/)
[使用 Spring 5 的 WebFlux 开发反应式 Web 应用](https://www.ibm.com/developerworks/cn/java/spring5-webflux-reactive/index.html)
[响应式编程介绍](http://wiki.jikexueyuan.com/project/android-weekly/issue-145/introduction-to-RP.html)
[响应式编程总览](http://emacoo.cn/backend/reactive-overview/)



