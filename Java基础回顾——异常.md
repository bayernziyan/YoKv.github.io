---
title: 异常
date: 2017-08-20 15:30:56
categories: 
- JavaSE
---

## 概念
<!--more-->
[来源](http://www.cnblogs.com/dolphin0520/p/3769804.html)
在Java中异常被当做对象来处理，根类是java.lang.Throwable类，在Java中定义了很多异常类（如OutOfMemoryError、NullPointerException、IndexOutOfBoundsException等），这些异常类分为两大类：Error和Exception。
Error是无法处理的异常，比如OutOfMemoryError，一般发生这种异常，JVM会选择终止程序。因此我们编写程序时不需要关心这类异常。
Exception，也就是我们经常见到的一些异常情况，比如NullPointerException、IndexOutOfBoundsException，这些异常是我们可以处理的异常。
Exception类的异常包括checked exception和unchecked exception（unchecked exception也称运行时异常RuntimeException，当然这里的运行时异常并不是前面我所说的运行期间的异常，只是Java中用运行时异常这个术语来表示，Exception类的异常都是在运行期间发生的）。
unchecked exception（非检查异常），也称运行时异常（RuntimeException），比如常见的NullPointerException、IndexOutOfBoundsException。对于运行时异常，java编译器不要求必须进行异常捕获处理或者抛出声明，由程序员自行决定。
checked exception（检查异常），也称非运行时异常（运行时异常以外的异常就是非运行时异常），java编译器强制程序员必须进行捕获处理，比如常见的IOExeption和SQLException。对于非运行时异常如果不进行捕获或者抛出声明处理，编译都不会通过。

## 异常使用误区
[来源](https://www.ibm.com/developerworks/cn/java/j-lo-exception-misdirection)

### 对代码层次结构的污染
我们经常将代码分 Service、Business Logic、DAO 等不同的层次结构，DAO 层中会包含抛出异常的方法

```java
public Customer retrieveCustomerById(Long id) throw SQLException {
 //根据 ID 查询数据库
}
```
上面这段代码咋一看没什么问题，但是从设计耦合角度仔细考虑一下，这里的 SQLException 污染到了上层调用代码，调用层需要显式的利用 try-catch 捕捉，或者向更上层次进一步抛出。根据设计隔离原则，我们可以适当修改成：
```java
public Customer retrieveCustomerById(Long id) {
     try{
            //根据 ID 查询数据库
     }catch(SQLException e){
            //利用非检测异常封装检测异常，降低层次耦合
            throw new RuntimeException(SQLErrorCode, e);
     }finally{
            //关闭连接，清理资源
     }
}
```

### 将异常包含在循环语句块中

### 利用 Exception 捕捉所有潜在的异常
一段方法执行过程中抛出了几个不同类型的异常，为了代码简洁，利用基类 Exception 捕捉所有潜在的异常，如下例所示：
```java
public void retrieveObjectById(Long id){
    try{
        //…抛出 IOException 的代码调用
        //…抛出 SQLException 的代码调用
    }catch(Exception e){
        //这里利用基类 Exception 捕捉的所有潜在的异常，如果多个层次这样捕捉，会丢失原始异常的有效信息
        throw new RuntimeException(“Exception in retieveObjectById”, e);
    }
}
```
可以重构成
```java
public void retrieveObjectById(Long id){
    try{
        //..some code that throws RuntimeException, IOException, SQLException
    }catch(IOException e){
        //仅仅捕捉 IOException
        throw new RuntimeException(/*指定这里 IOException 对应的错误代码*/code,“Exception in retieveObjectById”, e);
    }catch(SQLException e){
        //仅仅捕捉 SQLException
        throw new RuntimeException(/*指定这里 SQLException 对应的错误代码*/code,“Exception in retieveObjectById”, e);
    }
}
```

### 多层次封装抛出非检测异常
如果我们一直坚持不同类型的异常一定用不同的捕捉语句，那大部分例子可以绕过这一节了。但是如果仅仅一段代码调用会抛出一种以上的异常时，很多时候没有必要每个不同类型的 Exception 写一段 catch 语句，对于开发来说，任何一种异常都足够说明了程序的具体问题。
```
try{
    //可能抛出 RuntimeException、IOExeption 或者其它；
    //注意这里和误区六的区别，这里是一段代码抛出多种异常。以上是多段代码，各自抛出不同的异常
}catch(Exception e){
    //一如既往的将 Exception 转换成 RuntimeException，但是这里的 e 其实是 RuntimeException 的实例，已经在前段代码中封装过
    throw new RuntimeException(/**/code, /**/, e);
}
```
如果我们如上例所示，将所有的 Exception 再转换成 RuntimeException，那么当 Exception 的类型已经是 RuntimeException 时，我们又做了一次封装。将 RuntimeException 又重新封装了一次，进而丢失了原有的 RuntimeException 携带的有效信息。
解决办法是我们可以在 RuntimeException 类中添加相关的检查，确认参数 Throwable 不是 RuntimeException 的实例。如果是，将拷贝相应的属性到新建的实例上。或者用不同的 catch 语句块捕捉 RuntimeException 和其它的 Exception。个人偏好方式一，好处不言而喻。

### 多层次打印异常
我们先看一下下面的例子，定义了 2 个类 A 和 B。其中 A 类中调用了 B 类的代码，并且 A 类和 B 类中都捕捉打印了异常。
```java
public class A {
 private static Logger logger = LoggerFactory.getLogger(A.class);
 public void process(){
     try{
     //实例化 B 类，可以换成其它注入等方式
     B b = new B();
     b.process();
     //other code might cause exception
    } catch(XXXException e){
       //如果 B 类 process 方法抛出异常，异常会在 B 类中被打印，在这里也会被打印，从而会打印 2 次
       logger.error(e);
       throw new RuntimeException(/* 错误代码 */ errorCode, /*异常信息*/msg, e);
       }
    }
}
public class B{
 private static Logger logger = LoggerFactory.getLogger(B.class);
    public void process(){
        try{
            //可能抛出异常的代码
        }
        catch(XXXException e){
            logger.error(e);
            throw new RuntimeException(/* 错误代码 */ errorCode, /*异常信息*/msg, e);
        }
 }
}
```
同一段异常会被打印 2 次。如果层次再复杂一点，不去考虑打印日志消耗的系统性能，仅仅在异常日志中去定位异常具体的问题已经够头疼的了。
其实打印日志只需要在代码的最外层捕捉打印就可以了，异常打印也可以写成 AOP，织入到框架的最外层。

### 异常包含的信息不能充分定位问题
异常不仅要能够让开发人员知道哪里出了问题，更多时候开发人员还需要知道是什么原因导致的问题，我们知道 java .lang.Exception 有字符串类型参数的构造方法，这个字符串可以自定义成通俗易懂的提示信息。
简单的自定义信息开发人员只能知道哪里出现了异常，但是很多的情况下，开发人员更需要知道是什么参数导致了这样的异常。这个时候我们就需要将方法调用的参数信息追加到自定义信息中。下例只列举了一个参数的情况，多个参数的情况下，可以单独写一个工具类组织这样的字符串。
```java
public void retieveObjectById(Long id){
    try{
        //..some code that throws SQLException
   }catch(SQLException ex){
        //将参数信息添加到异常信息中
        throw new RuntimeException(“Exception in retieveObjectById with Object Id :”+ id, ex);
   }
}
```

### 不能预知潜在的异常
在写代码的过程中，由于对调用代码缺乏深层次的了解，不能准确判断是否调用的代码会产生异常，因而忽略处理。在产生了 Production Bug 之后才想起来应该在某段代码处添加异常补捉，甚至不能准确指出出现异常的原因。这就需要开发人员不仅知道自己在做什么，而且要去尽可能的知道别人做了什么，可能会导致什么结果，从全局去考虑整个应用程序的处理过程。这些思想会影响我们对代码的编写和处理。

## 一些好的实践

* 只在必要使用异常的地方才使用异常，不要用异常去控制程序的流程
* 检查异常和非检查异常的选择
* 不要把上层类的异常放在最前面的catch块
* 异常处理尽量放在高层进行
* 在finally中释放资源（JDK7的实现）