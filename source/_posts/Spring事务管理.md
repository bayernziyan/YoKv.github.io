---
title: Spring事务管理
date: 2017-10-31 23:58:46
categories: 
- Java框架
---

##  事务传播属性
<!--more-->

```java
public enum Propagation {
    REQUIRED(0),
    SUPPORTS(1),
    MANDATORY(2),
    REQUIRES_NEW(3),
    NOT_SUPPORTED(4),
    NEVER(5),
    NESTED(6);

    private final int value;

    private Propagation(int value) {
        this.value = value;
    }

    public int value() {
        return this.value;
    }
}

```

1) REQUIRED ，这个是默认的属性 
Support a current transaction, create a new one if none exists. 
如果存在一个事务，则支持当前事务。如果没有事务则开启一个新的事务。 
被设置成这个级别时，会为每一个被调用的方法创建一个逻辑事务域。如果前面的方法已经创建了事务，那么后面的方法支持当前的事务，如果当前没有事务会重新建立事务。 

2) MANDATORY 
Support a current transaction, throw an exception if none exists.支持当前事务，如果当前没有事务，就抛出异常。 

3) NEVER 
Execute non-transactionally, throw an exception if a transaction exists. 
以非事务方式执行，如果当前存在事务，则抛出异常。 

4) NOT_SUPPORTED 
Execute non-transactionally, suspend the current transaction if one exists. 
以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。 

5) REQUIRES_NEW 
Create a new transaction, suspend the current transaction if one exists. 
新建事务，如果当前存在事务，把当前事务挂起。 
如图所示： 

6) SUPPORTS 
Support a current transaction, execute non-transactionally if none exists. 
支持当前事务，如果当前没有事务，就以非事务方式执行。 

7) NESTED 
Execute within a nested transaction if a current transaction exists, behave like PROPAGATION_REQUIRED else. 
支持当前事务，新增Savepoint点，与当前事务同步提交或回滚。 
嵌套事务一个非常重要的概念就是内层事务依赖于外层事务。外层事务失败时，会回滚内层事务所做的动作。而内层事务操作失败并不会引起外层事务的回滚。 

## 事务隔离

```java
public enum Isolation {
    DEFAULT(-1),
    READ_UNCOMMITTED(1),
    READ_COMMITTED(2),
    REPEATABLE_READ(4),
    SERIALIZABLE(8);

    private final int value;

    private Isolation(int value) {
        this.value = value;
    }

    public int value() {
        return this.value;
    }
}
```
### 存在的问题
* Dirty Reads 脏读 ：
一个事务正在对数据进行更新操作，但是更新还未提交，另一个事务这时也来操作这组数据，并且读取了前一个事务还未提交的数据，而前一个事务如果操作失败进行了回滚，后一个事务读取的就是错误数据，这样就造成了脏读。
* Non-Repeatable Reads 不可重复读 ：
一个事务多次读取同一数据，在该事务还未结束时，另一个事务也对该数据进行了操作，而且在第一个事务两次次读取之间，第二个事务对数据进行了更新，那么第一个事务前后两次读取到的数据是不同的，这样就造成了不可重复读。
* Phantom Reads 幻像读 ：
第一个数据正在查询符合某一条件的数据，这时，另一个事务又插入了一条符合条件的数据，第一个事务在第二次查询符合同一条件的数据时，发现多了一条前一次查询时没有的数据，仿佛幻觉一样，这就是幻像读。


* DEFAULT：
这是一个PlatfromTransactionManager默认的隔离级别，使用数据库默认的事务隔离级别。 
* READ_UNCOMMITTED：
这是事务最低的隔离级别，它充许别外一个事务可以看到这个事务未提交的数据。这种隔离级别会产生脏读，不可重复读和幻像读。 
大部分数据库缺省的事物隔离级别都不会出现这种状况) 
* READ_COMMITTED ：
保证一个事务修改的数据提交后才能被另外一个事务读取。另外一个事务不能读取该事务未提交的数据。这种事务隔离级别可以避免脏读出现，但是可能会出现不可重复读和幻像读。
* _REPEATABLE_READ：  
这种事务隔离级别可以防止脏读，不可重复读。但是可能出现幻像读。 
* SERIALIZABLE ：
这是花费最高代价但是最可靠的事务隔离级别。事务被处理为顺序执行。除了防止脏读，不可重复读外，还避免了幻读。 



