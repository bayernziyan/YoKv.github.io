---
title: 一次分享会（Reactive）
date: 2018-02-05 17:30:27
categories:
- 项目实践
---

## 主题——响应式编程（Reactive）
### 介绍
<!--more-->
最近看到Dubbo 3.0将要在3月底发布，Spring Boot在经历了大半年的2.0里程碑版本（stands for milestone）迭代后，终于发布了RC版本（stands for Release Candidate），Release版本（正式版本）指日可待了，同时Spring Cloud的2.0版本也在稳步迭代。这些版本的迭代都包含了Reactive的内容。
* [**Dubbo3.0变化：**](https://mp.weixin.qq.com/s/PpH9xoj0FZAbjKHkA57AAw)
	Dubbo 3.0 将以 Streaming 为内核,重点是在服务治理和编程模型上,而编程模型的革新，其实就是使用了“反应式编程”模式（Reactive Programming）。在 Dubbo 3.0 中，reactive 将成为核心，会做到客户端、服务端、缓存和数据库，全程无阻塞。
* [**Spring2.0变化：**](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Release-Notes)
	1. Spring Framework 5.0
		Spring Boot 2.0 builds on and requires Spring Framework 5.0. There are a number of nice refinements in Spring Framework 5.0 including extensive support for **building reactive applications**. Please refer to the Spring Framework Wiki for details.
	2. WebFlux and WebFlux.fn support
		Spring Boot 2.0 provides a new starter for supporting the Reactive Spring web frameworks, for both annotation and functional based variants. spring-boot-starter-webflux brings WebFlux itself, plus Reactor Netty as a default web engine (spring-boot-starter-reactor-netty).
	3. Reactive data support
		Spring Boot 2.0 provides auto-configuration for the following data store with reactive support:MongoDB (spring-boot-starter-data-mongodb-reactive),Redis (spring-boot-starter-data-redis-reactive),Cassandra (spring-boot-starter-data-cassandra-reactive)
	4. Reactive server customisation
		 Customizers for Jetty, Tomcat, and Undertow are now called when configuring a reactive web server.
	5. Reactive Couchbase support
		Support for Spring Data reactive repositories is available for Couchbase and a spring-boot-starter-data-couchbase-reactive is available to easily get started.
* **java9**:
	Reactive Streams

### 关键词
* **事件驱动**
* **stream**
* **发布订阅模型**
* **backpressure**
* **非阻塞**
* **异步**

### 概念理解 
#### stream
传统的编程模式以对象为基础,响应式以事件流为基础。任何东西都可以是一个 Stream：变量、用户输入、属性、Cache、数据结构等。在这个基础上，有令人惊艳的函数去组合、创建、过滤这些 Streams。这就是函数式魔法的用武之地。Stream 能接受一个，甚至多个 Stream 为输入。你可以融合两个 Stream，也可以从一个 Stream 中过滤出你感兴趣的 Events 以生成一个新的 Stream，还可以把一个 Stream 中的数据值映射到一个新的 Stream 中。

#### 事件驱动(消息驱动)
响应式编程来源于数据流和变化的传播，意味着由底层的执行模型负责通过数据流来自动传播变化。这句话让我联想到vue.js的双向绑定，换句话说，vue.js来源于数据流和变化，可以不使用代码操作dom，自动传播数据变化（改变页面）。

#### 发布订阅模型
在传统的编程范式中，我们一般通过迭代器（Iterator）模式来遍历一个序列。这种遍历方式是由调用者来控制节奏的，采用的是拉的方式。每次由调用者通过 next()方法来获取序列中的下一个值。使用反应式流时采用的则是推的方式，即常见的发布者-订阅者模式。
当发布者有新的数据产生时，这些数据会被推送到订阅者来进行处理。在反应式流上可以添加各种不同的操作来对数据进行处理，形成数据处理链。这个以声明式的方式添加的处理链只在订阅者进行订阅操作时才会真正执行。
在Java 9中提供了Reactive Streams。它是“一种规范，为基于非阻塞回压的异步流处理提供了标准”，包含了TCK工具套件和四个简单接口（Publisher、Subscriber、Subscription和Processor）的规范。

#### 负压/背压/回压(backpressure)
一个重要概念是**负压/背压/回压**。在基本的消息推送模式中，当消息发布者产生数据的速度过快时，会使得消息订阅者的处理速度无法跟上产生的速度，从而给订阅者造成很大的压力。当压力过大时，有可能造成订阅者本身的奔溃，所产生的级联效应甚至可能造成整个系统的瘫痪。负压的作用在于提供一种从订阅者到生产者的反馈渠道。订阅者声明其一次所能处理的消息数量，而发布者就只会产生相应数量的消息。这实际上变成了推拉结合的模式。

### 概念总结
一句话总结：响应式编程是一种按流水线模式组织处理数据流的协程计算模型。
#### 优点
* 基于数据流，专注于逻辑，代码干净优雅
* 无需关心同步、等待、唤醒、锁等并发问题
* 背压传递，每个动作都可以调节（缓冲、消减、合并）其上下游动作的压力，在链条上传递压力

#### 缺点
* 难调试
* 基于ThreadLocal的事务控制,session会失效
* 一旦阻塞，可能导致整个服务崩溃

### java原生实现原理
```java

```
#### 为什么不是callable

rxjava原理
#### 非阻塞
Mono作为一个Publisher，它往往代表着一个耗费资源的任务（在IO、延迟等方面），意识到这点是理解回压的关键：如果不对其进行订阅，你就不需要为之付出任何代价。因为Mono经常跟具有回压的Flux一起被编排到一个响应式链上，来自多个异步数据源的结果有可能被组合到一起，这种按需触发的能力是避免阻塞的关键。




## 代码实现
### 两大主流类库支持
#### reactor(Spring5中响应式编程的基础)
[reactor官网](http://projectreactor.io/)
reactor执行链：
1. 终端向上游订阅，终端上游再向其上游订阅直到顶端
2. 顶端响应下游订阅，顶端下游再响应下游订阅直到终端
3. 终端向上游发起数据请求，终端上游再向上游发起数据请求直到顶端
4. 顶端响应下游请求返回指定数量数据，顶端下游处理结束，再向其下游响应数据，直到终端
5. 终端再次发起数据请求，循环4，直到所有数据处理完毕，收到上游完成信号
6. 终端向上游发起取消订阅，不断向上游传递直到顶端，结束当前流式处理

#### rxjava
[rxjava官网](http://reactivex.io/)
*TODO**

### 主流框架
#### spring5与 spring boot2.0
[Spring WebFlux Framework 官方文档](https://docs.spring.io/spring-boot/docs/2.0.0.RC1/reference/htmlsingle/#boot-features-webflux)
##### annotation方式实现web
[源代码地址](https://github.com/YoKv/microservices-practise/tree/master/microservices/service666)

Controller
```java
@RestController
public class NormalController {

  private final FooService fooService;

  @Autowired
  public NormalController(final FooService fooService) {
    this.fooService = fooService;
  }

  /**
   * 异常处理
   */
  @ResponseStatus(value = HttpStatus.NOT_FOUND, reason = "Resource not found")
  @ExceptionHandler(Exception.class)
  public void notFound() {
  }

  @GetMapping("/list")
  public Flux<Foo> list() {
    return Flux.fromStream(fooService.findAll().stream());
  }

  @GetMapping("/{id}")
  public Mono<Foo> getById(@PathVariable("id") final Long id) {
    return Mono.justOrEmpty(fooService.findById(id));
  }

  //FIXME
  @PostMapping("/saveBatch")
  public Flux<Foo> save(@RequestBody final Flux<Foo> foobar) {
    return foobar.doOnNext(foo -> fooService.save(foo));
  }
  //FIXME
  @PutMapping("/update")
  public Mono<Foo> update(@RequestBody final Foo foo) {
    Objects.requireNonNull(foo);
    return Mono.just(fooService.save(foo));
  }

  @DeleteMapping("/{id}")
  public Mono<Foo> delete(@PathVariable("id") final Long id) {
    return Mono.justOrEmpty(fooService.delete(id));
  }

}
```

service
```java

@Service("fooService")
public class FooServiceImpl implements FooService {

  //假装数据库
  private Map<Long, Foo> dataSource;
  private AtomicLong atomicLong;

  public FooServiceImpl() {
    dataSource = new ConcurrentHashMap<>();
    atomicLong = new AtomicLong(1);
    Foo foo = new Foo();
    foo.setId(atomicLong.get());
    foo.setVar1("var1");
    foo.setVar2("var2");
    dataSource.put(atomicLong.get(), foo);
  }

  @Override
  public List<Foo> findAll() {
    return new ArrayList<>(dataSource.values());
  }

  @Override
  public Foo findById(Long id) {
    return dataSource.get(id);
  }

  @Override
  public Foo save(Foo foo) {
    Long id = foo.getId();
    if (null == id) {
      id = atomicLong.incrementAndGet();
      foo.setId(id);
    }
    dataSource.put(id, foo);
    return foo;
  }

  @Override
  public Foo delete(Long id) {
    return dataSource.remove(id);
  }
}

```

#### vert.x
[Vert.x 官网](http://vertx.io/)
[Vert.x 官方文档中文翻译](https://vertxchina.github.io/vertx-translation-chinese/)
[Vert.x 性能](https://www.techempower.com/benchmarks/#section=data-r8&hw=i7&test=plaintext)
Vert.x框架基于事件和异步，依托于全异步Java服务器Netty，并扩展了很多其他特性，以其轻量、高性能、支持多语言开发而备受开发者青睐。
Vert.x框架也提供了RxJava2的支持
##### 代码
[项目代码](https://github.com/YoKv/aiyo/tree/master/aiyo-service-steam)

### 参考资料
[图解rx系列api](http://rxmarbles.com/#merge)
[Wikipedia](https://en.wikipedia.org/wiki/Reactive_programming)
[概念介绍gist](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)
[stackoverflow响应式编程讨论](https://stackoverflow.com/questions/1028250/what-is-functional-reactive-programming)
[其他优质分享(有视频)](https://academy.realm.io/cn/posts/droidcon-gomez-functional-reactive-programming/)
[使用 Spring 5 的 WebFlux 开发反应式 Web 应用](https://www.ibm.com/developerworks/cn/java/spring5-webflux-reactive/index.html)
[响应式编程介绍](https://www.ibm.com/developerworks/cn/java/j-cn-with-reactor-response-encode/index.html)
[响应式编程总览](http://emacoo.cn/backend/reactive-overview/)


