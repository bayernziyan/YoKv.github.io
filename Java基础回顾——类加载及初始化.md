---
title: 类加载及初始化
date: 2017-08-21 10:00:57
categories: 
- JavaSE
---


## ClassLoader
<!--more-->
[来源](http://blog.csdn.net/xyang81/article/details/7292380)

### 什么是ClassLoader?
Java程序都是由若干个.class文件组织而成的一个完整的Java应用程序，当程序在运行时，即会调用该程序的一个入口函数来调用系统的相关功能，而这些功能都被封装在不同的class文件当中，所以经常要从这个class文件中要调用另外一个class文件中的方法，如果另外一个文件不存在的，则会引发系统异常。而程序在启动的时候，并不会一次性加载程序所要用的所有class文件，而是根据程序的需要，通过Java的类加载机制（ClassLoader）来动态加载某个class文件到内存当中的，从而只有class文件被载入到了内存之后，才能被其它class所引用。所以ClassLoader就是用来动态加载class文件到内存当中用的。

### Java默认提供的三个ClassLoader
* BootStrap ClassLoader：称为启动类加载器，是Java类加载层次中最顶层的类加载器，负责加载JDK中的核心类库，如：rt.jar、resources.jar、charsets.jar等，可通过如下程序获得该类加载器从哪些地方加载了相关的jar或class文件
* Extension ClassLoader：称为扩展类加载器，负责加载Java的扩展类库，默认加载JAVA_HOME/jre/lib/ext/目下的所有jar。
* App ClassLoader：称为系统类加载器，负责加载应用程序classpath目录下的所有jar和class文件。

除了Java默认提供的三个ClassLoader之外，用户还可以根据需要定义自已的ClassLoader，而这些自定义的ClassLoader都必须继承自java.lang.ClassLoader类，也包括Java提供的另外二个ClassLoader（Extension ClassLoader和App ClassLoader）在内，但是Bootstrap ClassLoader不继承自ClassLoader，因为它不是一个普通的Java类，底层由C++编写，已嵌入到了JVM内核当中，当JVM启动后，Bootstrap ClassLoader也随着启动，负责加载完核心类库后，并构造Extension ClassLoader和App ClassLoader类加载器。

## ClassLoader加载类的原理
### 原理介绍
 ClassLoader使用的是双亲委托模型来搜索类的，每个ClassLoader实例都有一个父类加载器的引用（不是继承的关系，是一个包含的关系），虚拟机内置的类加载器（Bootstrap ClassLoader）本身没有父类加载器，但可以用作其它ClassLoader实例的的父类加载器。当一个ClassLoader实例需要加载某个类时，它会试图亲自搜索某个类之前，先把这个任务委托给它的父类加载器，这个过程是由上至下依次检查的，首先由最顶层的类加载器Bootstrap ClassLoader试图加载，如果没加载到，则把任务转交给Extension ClassLoader试图加载，如果也没加载到，则转交给App ClassLoader 进行加载，如果它也没有加载得到的话，则返回给委托的发起者，由它到指定的文件系统或网络等URL中加载该类。如果它们都没有加载到这个类时，则抛出ClassNotFoundException异常。否则将这个找到的类生成一个类的定义，并将它加载到内存当中，最后返回这个类在内存中的Class实例对象。

### 为什么要使用双亲委托这种模型
因为这样可以避免重复加载，当父亲已经加载了该类的时候，就没有必要子ClassLoader再加载一次。考虑到安全因素，我们试想一下，如果不使用这种委托模式，那我们就可以随时使用自定义的String来动态替代java核心api中定义的类型，这样会存在非常大的安全隐患，而双亲委托的方式，就可以避免这种情况，因为String已经在启动时就被引导类加载器（Bootstrcp ClassLoader）加载，所以用户自定义的ClassLoader永远也无法加载一个自己写的String，除非你改变JDK中ClassLoader搜索类的默认算法。

### 如何判定两个class是相同的
 JVM在判定两个class是否相同时，不仅要判断两个类名是否相同，而且要判断是否由同一个类加载器实例加载的。只有两者同时满足的情况下，JVM才认为这两个class是相同的。

## 定义自已的ClassLoader
### 既然JVM已经提供了默认的类加载器，为什么还要定义自已的类加载器呢？
因为Java中提供的默认ClassLoader，只加载指定目录下的jar和class，如果我们想加载其它位置的类或jar时，比如：我要加载网络上的一个class文件，通过动态加载到内存之后，要调用这个类中的方法实现我的业务逻辑。在这样的情况下，默认的ClassLoader就不能满足我们的需求了，所以需要定义自己的ClassLoader。
定义自已的类加载器分为两步：
* 继承java.lang.ClassLoader
* 重写父类的findClass方法

## 类的生命周期
1. 装载：加载，验证，准备，解析
2. 初始化
3. 使用：对象实例化，垃圾收集，对象终结
4. 卸载

## 初始化步骤
静态代码块、代码块、构造方法、成员变量、子父类的初始化
一般步骤：
1. final修饰的变量（？？？）
2. static 修饰的模块（static变量和static 块）  ---> 按照代码顺序依次执行。（先有变量，然后按顺序赋值）
3. 实例变量  及非static模块---> 按照代码顺序依次执行。
4. 构造函数 ---> 执行对应的构造函数。

复杂的父子类：

1. 父类static修饰的模块
2. 子类static修饰模块
3. 父类实例变量和非static块
4. 父类对应构造函数。当子类对应构造函数中没有显示调用时调用的是父类默认的构造函数。
5. 子类实例变量和非static块
6. 子类构造函数
