---
title: JDBC及数据库
date: 2017-08-31 07:58:46
categories: 
- Java Web
---


## JDBC
为了简化、统一对数据库的操作，定义了一套Java操作数据库的规范（接口），称之为JDBC。这套接口由数据库厂商去实现，这样，开发人员只需要学习jdbc接口，并通过jdbc加载具体的驱动，就可以操作数据库。

<!--more-->
[来源](http://www.cnblogs.com/xdp-gacl/tag/JavaWeb%E5%AD%A6%E4%B9%A0%E6%80%BB%E7%BB%93/default.html?page=2)
### JDBC 连接

```java

	//要连接的数据库URL
    String url = "jdbc:mysql://localhost:3306/test";
    //连接的数据库时使用的用户名
    String username = "root";
    //连接的数据库时使用的密码
    String password = "xxx";
    
    //1.加载驱动
    //DriverManager.registerDriver(new com.mysql.jdbc.Driver());不推荐使用这种方式来加载驱动
    Class.forName("com.mysql.jdbc.Driver");//推荐使用这种方式来加载驱动
    //2.获取与数据库的链接
    Connection conn = DriverManager.getConnection(url, username, password);
    
    //3.获取用于向数据库发送sql语句的statement
    Statement st = conn.createStatement();
    
    String sql = "select id,name,password,email,birthday from users";
    //4.向数据库发sql,并获取代表结果集的resultset
    ResultSet rs = st.executeQuery(sql);
    
    //5.取出结果集的数据
    while(rs.next()){
        System.out.println("id=" + rs.getObject("id"));
        System.out.println("name=" + rs.getObject("name"));
        System.out.println("password=" + rs.getObject("password"));
        System.out.println("email=" + rs.getObject("email"));
        System.out.println("birthday=" + rs.getObject("birthday"));
    }
    
    //6.关闭链接，释放资源
    rs.close();
    st.close();
    conn.close();

```
#### Connection类
Jdbc程序中的Connection，它用于代表数据库的链接，Collection是数据库编程中最重要的一个对象，客户端与数据库所有交互都是通过connection对象完成的，这个对象的常用方法：
* createStatement()：创建向数据库发送sql的statement对象。
* prepareStatement(sql) ：创建向数据库发送预编译sql的PrepareSatement对象。
* prepareCall(sql)：创建执行存储过程的callableStatement对象。
* setAutoCommit(boolean autoCommit)：设置事务是否自动提交。
* commit() ：在链接上提交事务。
* rollback() ：在此链接上回滚事务。

#### Statement类
Jdbc程序中的Statement对象用于向数据库发送SQL语句， Statement对象常用方法：
* executeQuery(String sql) ：用于向数据发送查询语句。
* executeUpdate(String sql)：用于向数据库发送insert、update或delete语句
* execute(String sql)：用于向数据库发送任意sql语句
* addBatch(String sql) ：把多条sql语句放到一个批处理中。
* executeBatch()：向数据库发送一批sql语句执行。

#### ResultSet类
Jdbc程序中的ResultSet用于代表Sql语句的执行结果。Resultset封装执行结果时，采用的类似于表格的方式。ResultSet 对象维护了一个指向表格数据行的游标，初始的时候，游标在第一行之前，调用ResultSet.next() 方法，可以使游标指向具体的数据行，进行调用方法获取该行的数据。
ResultSet还提供了对结果集进行滚动的方法：
* next()：移动到下一行
* Previous()：移动到前一行
* absolute(int row)：移动到指定行
* beforeFirst()：移动resultSet的最前面。
* afterLast() ：移动到resultSet的最后面。

### CRUD
Jdbc中的statement对象用于向数据库发送SQL语句，想完成对数据库的增删改查，只需要通过这个对象向数据库发送增删改查语句即可。
Statement对象的executeUpdate方法，用于向数据库发送增、删、改的sql语句，executeUpdate执行完后，将会返回一个整数(即增删改语句导致了数据库几行数据发生了变化)。
Statement.executeQuery方法用于向数据库发送查询语句，executeQuery方法返回代表查询结果的ResultSet对象。

PreperedStatement是Statement的子类，它的实例对象可以通过调用Connection.preparedStatement()方法获得，相对于Statement对象而言：PreperedStatement可以避免SQL注入的问题。
Statement会使数据库频繁编译SQL，可能造成数据库缓冲区溢出。PreparedStatement可对SQL进行预编译，从而提高数据库的执行效率。并且PreperedStatement对于sql中的参数，允许使用占位符的形式进行替换，简化sql语句的编写。

## 事务
当Jdbc程序向数据库获得一个Connection对象时，默认情况下这个Connection对象会自动向数据库提交在它上面发送的SQL语句。若想关闭这种默认提交方式，让多条SQL在一个事务中执行，可使用下列的JDBC控制事务语句
```java
	Connection.setAutoCommit(false);//开启事务(start transaction)
	Connection.rollback();//回滚事务(rollback)
	Connection.commit();//提交事务(commit)
```

在开发中，有时候可能需要手动设置事务的回滚点，在JDBC中使用如下的语句设置事务回滚点
```java
　　Savepoint sp = conn.setSavepoint();
　　Conn.rollback(sp);
　　Conn.commit();//回滚后必须通知数据库提交事务
```
### 四大特性(ACID)
* 原子性（Atomicity）:原子性是指事务是一个不可分割的工作单位，事务中的操作要么全部成功，要么全部失败。比如在同一个事务中的SQL语句，要么全部执行成功，要么全部执行失败
* 一致性（Consistency）:事务必须使数据库从一个一致性状态变换到另外一个一致性状态。以转账为例子，A向B转账，假设转账之前这两个用户的钱加起来总共是2000，那么A向B转账之后，不管这两个账户怎么转，A用户的钱和B用户的钱加起来的总额还是2000，这个就是事务的一致性。
* 隔离性（Isolation）:事务的隔离性是多个用户并发访问数据库时，数据库为每一个用户开启的事务，不能被其他事务的操作数据所干扰，多个并发事务之间要相互隔离。
* 持久性（Durability）:持久性是指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来即使数据库发生故障也不应该对其有任何影响

### 事务隔离
MySQL数据库共定义了四种隔离级别：
* Serializable(串行化)：可避免脏读、不可重复读、虚读情况的发生。
* Repeatable read(可重复读)：可避免脏读、不可重复读情况的发生。
* Read committed(读已提交)：可避免脏读情况发生。
* Read uncommitted(读未提交)：最低级别，以上情况均无法保证。
mysql数据库查询当前事务隔离级别：select @@tx_isolation


## 数据库连接池
数据库连接是一种关键的有限的昂贵的资源,这一点在多用户的网页应用程序中体现的尤为突出.对数据库连接的管理能显著影响到整个应用程序的伸缩性和健壮性,影响到程序的性能指标.数据库连接池正式针对这个问题提出来的.数据库连接池负责分配,管理和释放数据库连接,它允许应用程序重复使用一个现有的数据库连接,而不是重新建立一个。
数据库连接池在初始化时将创建一定数量的数据库连接放到连接池中, 这些数据库连接的数量是由最小数据库连接数来设定的.无论这些数据库连接是否被使用,连接池都将一直保证至少拥有这么多的连接数量.连接池的最大数据库连接数量限定了这个连接池能占有的最大连接数,当应用程序向连接池请求的连接数超过最大连接数量时,这些请求将被加入到等待队列中.

数据库连接池的最小连接数和最大连接数的设置要考虑到以下几个因素:
* 最小连接数:是连接池一直保持的数据库连接,所以如果应用程序对数据库连接的使用量不大,将会有大量的数据库连接资源被浪费.
* 最大连接数:是连接池能申请的最大连接数,如果数据库连接请求超过次数,后面的数据库连接请求将被加入到等待队列中,这会影响以后的数据库操作
* 如果最小连接数与最大连接数相差很大:那么最先连接请求将会获利,之后超过最小连接数量的连接请求等价于建立一个新的数据库连接.不过,这些大于最小连接数的数据库连接在使用完不会马上被释放,他将被放到连接池中等待重复使用或是空间超时后被释放.

相关框架：Apache-DBCP，C3P0，Proxool，druid。