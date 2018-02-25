---
title: vert.x Core
date: 2018-01-09 17:30:27
categories:
- Java框架
---

##  Core功能
Vert.x Core 提供了下列功能:
* 编写 TCP 客户端和服务端
* 编写支持 WebSocket 的 HTTP 客户端和服务端
* 事件总线
* 共享数据 —— 本地的Map和分布式集群Map
* 周期性、延迟性动作
* 部署和撤销 Verticle 实例
* 数据报套接字
* DNS客户端
* 文件系统访问
* 高可用性
* 集群

<!--more-->

## Vert.x 对象
Vert.x Core中的功能相当底层.
 Vert.x对象是Vert.x 的控制中心，是做几乎一切事情的基础，包括创建客户端和服务器、获取事件总线的引用、设置定时器等等。
```java
Vertx vertx = Vertx.vertx();
```
Vert.x对象可以在创建 Vertx 对象时指定配置项，也可以创建集群模式的 Vert.x 对象：
```java
// 注意要添加对应的集群管理器依赖，详情见集群管理器章节
VertxOptions options = new VertxOptions();
Vertx.clusteredVertx(options, res -> {
  if (res.succeeded()) {
    Vertx vertx = res.result(); // 获取到了集群模式下的 Vertx 对象
    // 做一些其他的事情
  } else {
    // 获取失败，可能是集群管理器出现了问题
  }
});
```

## 重要注意点
* 流式（Fluent）的API是贯穿 Vert.x API 中的一个通用模式
* Vert.x 的 API 大部分都是事件驱动的
* 黄金法则：不要阻塞Event Loop

### 事件驱动
在多数情况下，Vert.x使用被称为 Event Loop 的线程来调用处理器。

####  Reactor 模式
在一个标准的反应器实现中，有 一个独立的 Event Loop 会循环执行，处理所有到达的事件并传递给处理器处理,Node.js 实现了这种模式

#### Multi-Reactor 模式
Vert.x的工作方式有所不同。每个 Vertx 实例维护的是 多个Event Loop 线程。默认情况下，会根据机器上可用的核数量来设置 Event Loop 的数量，可自行设置。这意味着 Vertx 进程能够在服务器上扩展

### 运行阻塞式代码
可以通过调用 executeBlocking 方法来指定阻塞式代码的执行以及阻塞式代码执行后处理结果的异步回调。默认的阻塞式代码会在 Vert.x 的 Worker Pool 中执行，通过 setWorkerPoolSize 配置。

### 异步协调
Vert.x 中的 Future 可以用来协调多个异步操作的结果。它支持并发组合（并行执行多个异步调用）和顺序组合（依次执行异步调用）。

#### 并发合并
* CompositeFuture.all
```java
Future<HttpServer> httpServerFuture = Future.future();
httpServer.listen(httpServerFuture.completer());

Future<NetServer> netServerFuture = Future.future();
netServer.listen(netServerFuture.completer());

CompositeFuture.all(httpServerFuture, netServerFuture).setHandler(ar -> {
  if (ar.succeeded()) {
    // 所有服务器启动完成
  } else {
    // 有一个服务器启动失败
  }
});
```
* CompositeFuture.any
```java
CompositeFuture.any(future1, future2).setHandler(ar -> {
  if (ar.succeeded()) {
    // 至少一个成功
  } else {
    // 所有的都失败
  }
});

```
* CompositeFuture.join
	join 方法的合并会等待所有的 Future 完成，无论成败。
```java
CompositeFuture.join(future1, future2, future3).setHandler(ar -> {
  if (ar.succeeded()) {
    // 所有都成功
  } else {
    // 至少一个失败
  }
});

```


* compose
	顺序合并
```java
FileSystem fs = vertx.fileSystem();
Future<Void> startFuture = Future.future();

Future<Void> fut1 = Future.future();
fs.createFile("/foo", fut1.completer());

fut1.compose(v -> {
  // fut1中文件创建完成后执行
  Future<Void> fut2 = Future.future();
  fs.writeFile("/foo", Buffer.buffer(), fut2.completer());
  return fut2;
}).compose(v -> {
  // fut2文件写入完成后执行
  fs.move("/foo", "/bar", startFuture.completer());
},
  // 如果任何一步失败，将startFuture标记成failed
  startFuture);
```
	三个操作被串起来了：
	1. 一个文件被创建（fut1）
	2. 一些东西被写入到文件（fut2）
	3. 文件被移走（startFuture）

## Verticle

### Verticle介绍
Verticle 是由 Vert.x 部署和运行的代码块。默认情况一个 Vert.x 实例维护了N（默认情况下N = CPU核数 x 2）个 Event Loop 线程。Verticle 实例可使用任意 Vert.x 支持的编程语言编写，而且一个简单的应用程序也可以包含多种语言编写的 Verticle。
一个应用程序通常是由在同一个 Vert.x 实例中同时运行的许多 Verticle 实例组合而成。不同的 Verticle 实例通过向** Event Bus **上发送消息来相互通信。

### 编写 Verticle

```java
public class MyVerticle extends AbstractVerticle {

  // Called when verticle is deployed
  // Verticle部署时调用
  public void start() {
  }

  // Optional - called when verticle is undeployed
  // 可选 - Verticle撤销时调用
  public void stop() {
  }

}
```

### Verticle 种类
* Stardand Verticle：这是最常用的一类 Verticle —— 它们永远运行在 Event Loop 线程上。
* Worker Verticle：这类 Verticle 会运行在 Worker Pool 中的线程上。一个实例绝对不会被多个线程同时执行。
* Multi-Threaded Worker Verticle：这类 Verticle 也会运行在 Worker Pool 中的线程上。一个实例可以由多个线程同时执行（因此需要开发者自己确保线程安全）。

### Context 对象
当 Vert.x 传递一个事件给处理器或者调用 Verticle 的 start 或 stop 方法时，它会关联一个 Context 对象来执行。通常来说这个 Context 会是一个 Event Loop Context，它绑定到了一个特定的 Event Loop 线程上。所以在该 Context 上执行的操作总是在同一个 Event Loop 线程中。对于运行内联的阻塞代码的 Worker Verticle 来说，会关联一个 Worker Context，并且所有的操作运都会运行在 Worker 线程池的线程上。


## Event Bus
Event Bus 是 Vert.x 的神经系统。

### 基本概念
#### 寻址
消息会被 Event Bus 发送到一个 地址(address)。

#### 处理器
消息在处理器（Handler）中被接收。您可以在某个地址上注册一个处理器来接收消息。

### 发布/订阅消息
Event Bus支持 发布消息 功能。
消息将被发布到一个地址中，发布意味着会将信息传递给 所有 注册在该地址上的处理器。这和 发布/订阅模式 很类似。
### 点对点模式/请求-响应模式
Event Bus也支持 点对点消息模式。
消息将被发送到一个地址中，Vert.x将会把消息分发到某个注册在该地址上的处理器。若这个地址上有不止一个注册过的处理器，它将使用 不严格的轮询算法 选择其中一个。
点对点消息传递模式下，可在消息发送的时候指定一个应答处理器（可选）。
当接收者收到消息并且已经被处理时，它可以选择性决定回复该消息，若选择回复则绑定的应答处理器将会被调用。当发送者收到回复消息时，它也可以回复，这个过程可以不断重复。通过这种方式可以允许在两个不同的 Verticle 之间设置一个对话窗口。这种消息模式被称作 请求-响应 模式。

### 尽力传输
Vert.x会尽它最大努力去传递消息，并且不会主动丢弃消息。这种方式称为 尽力传输(Best-effort delivery)。

### 消息类型
Vert.x 默认允许任何基本/简单类型、String 或 Buffer 作为消息发送。不过在 Vert.x 中的通常做法是使用 JSON 格式来发送消息，
自定义类型要配合消息编解码器使用。

### 集群模式的 Event Bus
```java
VertxOptions options = new VertxOptions();
Vertx.clusteredVertx(options, res -> {
  if (res.succeeded()) {
    Vertx vertx = res.result();
    EventBus eventBus = vertx.eventBus();
    System.out.println("We now have a clustered event bus: " + eventBus);
  } else {
    System.out.println("Failed: " + res.cause());
  }
});
```

