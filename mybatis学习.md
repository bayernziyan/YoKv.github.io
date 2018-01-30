---
title: mybatis学习
date: 2018-01-30 17:30:27
categories: 
- Java框架
---


## 前言
基于[官网教程](http://www.mybatis.org/mybatis-3/zh/getting-started.html)整理
<!--more-->

## 从 XML 中构建 SqlSessionFactory
每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为中心的。SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。而 SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先定制的 Configuration 的实例构建出 SqlSessionFactory 的实例。

从 XML 文件中构建 SqlSessionFactory 的实例非常简单，建议使用类路径下的资源文件进行配置。但是也可以使用任意的输入流(InputStream)实例，包括字符串形式的文件路径或者 file:// 的 URL 形式的文件路径来配置。MyBatis 包含一个名叫 Resources 的工具类，它包含一些实用方法，可使从 classpath 或其他位置加载资源文件更加容易。
```java
String resource = "org/mybatis/example/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```
XML 配置文件（configuration XML）中包含了对 MyBatis 系统的核心设置，包含获取数据库连接实例的数据源（DataSource）和决定事务作用域和控制方式的事务管理器（TransactionManager）。

## 不使用 XML 构建 SqlSessionFactory

```java
DataSource dataSource = BlogDataSourceFactory.getBlogDataSource();
TransactionFactory transactionFactory = new JdbcTransactionFactory();
Environment environment = new Environment("development", transactionFactory, dataSource);
Configuration configuration = new Configuration(environment);
configuration.addMapper(BlogMapper.class);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(configuration);
```
## 从 SqlSessionFactory 中获取 SqlSession
```java
SqlSession session = sqlSessionFactory.openSession();
try {
  Blog blog = (Blog) session.selectOne("org.mybatis.example.BlogMapper.selectBlog", 101);
} finally {
  session.close();
}
```

```java
SqlSession session = sqlSessionFactory.openSession();
try {
  Blog blog = (Blog) session.selectOne("org.mybatis.example.BlogMapper.selectBlog", 101);
} finally {
  session.close();
}
```


## JDBC, Hibernate，Mybatis比较

### JDBC操作数据库步骤
1. 使用JDBC编程需要连接数据库，注册驱动和数据库信息
2. 操作Connection,打开Statement对象
3. 通过Statement执行SQL,返回结果到ResultSet对象
4. 使用ResultSet读取数据，然后通过代码转换为具体的POJO对象
5. 关闭数据库相关资源

### ORM模型
	> 对象关系映射(Object Relational Mapping),
	简单的说ORM框架就是数据库的表和简单Java对象的映射关系模型，
	我们通过这层映射关系就可以简单迅速地把数据库表的数据转化为POJO,
	
	ORM取代了JDBC,所有的ORM模型都是基于JDBC进行封装的，
	不同ORM模型对JDBC封装的强度是不一样的
### Hibernate优势
     >  消除了代码的映射规则，它全部被分离到了XML或者注解里面去配置
       无需再管理数据库连接，它也可以配置在XML里面
       一个会话中，不要操作多个对象，只要操作Session对象即可
       关闭资源只需要关闭一个Session便可


### Hibernate缺陷
      > 全表映射带来了不便，比如更新时需要发送所有的字段
       无法根据不同的条件组装不同的SQL
       对多表关联和复杂SQL查询支持较差，需要自己写SQL，返回后，需要自己将数据组装为POJO
       不能有效支持存储过程
       虽然有HQL，但是性能较差， 大型互联网系统往往需要优化SQL，而hibernate做不到

### Mybatis优势
    >  为了解决Hibernate的不足，一个半自动映射的框架MyBatis应运而生
      mybatis是一个java持久层框架，它封装少、高性能·可优化、
      维护简单等优点成为了目前java移动互联网网站服务的首选持久层框架，
      它特别适合分布式和大数据网络数据库编程
      之所以称它为半自动，是因为它需要手工匹配提供POJO、SQL和映射关系，
      而全表映射的Hibernate只需要提供POJO和映射关系便可

### Mybatis执行流程
     >  Mybatis是XML与POJO之间的映射关系 
      Hibernate是数据库表与POJO之间的映射关系

	> MyBatis应用程序根据XML配置文件创建SqlSessionFactory，
	SqlSessionFactory在根据配置，配置来源于两个地方，
	一处是配置文件，一处是Java代码的注解，
	获取一个SqlSession。
	SqlSession包含了执行sql所需要的所有方法，
	可以通过SqlSession实例直接运行映射的sql语句，
	完成对数据的增删改查和事务提交等，
	用完之后关闭SqlSession。

### Mybatis核心接口和类的结构


### Mybatis核心类的生命周期
* sqlSessionFactoryBuilder
     >   这个类可以被初始 、 使用和丢弃 ， 如果你已经创建好了一个 SqlSessionFactory 后就不用再保留它 。
       因此 ，SqlSessionFactoryBuilder 的最好作用域是方法体内，比如说定义一个方法变量。
       你可以重复使 用SqlSessionFactoryBuilder 生成多个SqlSessionFactory 实例 ， 但是最好不要强行保留 ，
       因为 XML 的解析资源要用来做其它更重要的事。

* sqlSessionFactory
     >   一旦创建， SqlSessionFactory 就会在整个应用过程中始终存在。所以没有理由去销毁和再创建它，
       一个应用运行中也不建议多次创建 SqlSessionFactory 。 如果真的那样做 ， 会显得很拙劣 。
       因此 SqlSessionFactor y最好的作用域是 Application 。可以有多种方法实现。
       最简单的方法是单例模式或者是静态单例模式 。 然而这既不是广泛赞成和好用的 。 
        反而 ， 使用 Google Guice 或 Spring 来进行依赖反射会更好 。 
        这些框架允许你生成管理器来管理 SqlSessionFactory 的单例生命周期

* sqlSession
      >   每个线程应该有自己的sqlSession实例。(思考为什么？) 
        最佳范围是请求和方法范围。
        不能将实例的引用放到一个类的静态字段，甚至实例字段中。
        不能放到任何类型的管理范围中，例如httpSession中。  sqlSession需要明确关联

### Mybatis基本要素
configuration.xml 是系统的核心配置文件，包含数据源和事务管理器等设置和属性信息，
XML文档结构如下：
* configuration 配置 
* properties 可以配置在Java 属性配置文件中
* settings 修改 MyBatis 在运行时的行为方式
* typeAliases  为 Java 类型命名一个短的名字
* typeHandlers 类型处理器 
* objectFactory 对象工厂 
* plugins 插件 
* environments 环境 
	 environment 环境变量 
	  1. transactionManager 事务管理器 
	  2. dataSource 数据源 
* mappers 映射器 

```xml
<?xml version="1.0" encoding="UTF-8" ?> 
<!DOCTYPE configuration PUBLIC “-//mybatis.org//DTD Config 3.0//EN”"http://mybatis.org/dtd/mybatis-3-config.dtd"> 
<configuration> 
    <setting>
        <setting name="mapUnderscoreToCamelCase" value="true"/>    
    </setting>
    <properties resource="jdbc.properties"/>    
    <typeAliases>  
        <typeAlias alias="User" type="xxx.mybatis.model.User"/>  
    </typeAliases>  

    <environments default="development"> 
        <environment id="development"> 
        <transactionManager type="JDBC"/> 
            <dataSource type="POOLED"> 
             <property name="driver" value="${datasource.driverClassName}" /> 
             <property name="url" value="${datasource.url}" />
             <property name="username" value="${datasource.username}" /> 
             <property name="password" value="${datasource.password}" />            
            </dataSource> 
        </environment> 
    </environments> 

    <typeHandlers>        <package name=“xxx.mybatis.typehandler”/>    </typeHandlers>      

    <mappers> 
        <mapper resource="xxx/mybatis/model/User.xml"/> 
    </mappers> 
</configuration> 

```

### 基础配置文件

```xml

<settings>  
        <!-- 该配置影响的所有映射器中配置的缓存的全局开关。默认值true -->  
      <setting name="cacheEnabled" value="true"/>  
      <!--延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置fetchType属性来覆盖该项的开关状态。默认值false  -->  
      <setting name="lazyLoadingEnabled" value="true"/>  
        <!-- 是否允许单一语句返回多结果集（需要兼容驱动）。 默认值true -->  
      <setting name="multipleResultSetsEnabled" value="true"/>  
      <!-- 使用列标签代替列名。不同的驱动在这方面会有不同的表现， 具体可参考相关驱动文档或通过测试这两种不同的模式来观察所用驱动的结果。默认值true -->  
      <setting name="useColumnLabel" value="true"/>  
      <!-- 允许 JDBC 支持自动生成主键，需要驱动兼容。 如果设置为 true 则这个设置强制使用自动生成主键，尽管一些驱动不能兼容但仍可正常工作（比如 Derby）。 默认值false  -->  
      <setting name="useGeneratedKeys" value="false"/>  
     <!--  指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示取消自动映射；PARTIAL 只会自动映射没有定义嵌套结果集映射的结果集。 FULL 会自动映射任意复杂的结果集（无论是否嵌套）。 -->   
     <!-- 默认值PARTIAL -->  
      <setting name="autoMappingBehavior" value="PARTIAL"/> 
      <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>  
     <!--  配置默认的执行器。SIMPLE 就是普通的执行器；REUSE 执行器会重用预处理语句（prepared statements）； BATCH 执行器将重用语句并执行批量更新。默认SIMPLE  -->  
      <setting name="defaultExecutorType" value="SIMPLE"/>  
      <!-- 设置超时时间，它决定驱动等待数据库响应的秒数。 -->  
      <setting name="defaultStatementTimeout" value="25"/>  
      <setting name="defaultFetchSize" value="100"/>  
      <!-- 允许在嵌套语句中使用分页（RowBounds）默认值False -->  
      <setting name="safeRowBoundsEnabled" value="false"/>  
      <!-- 是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 的类似映射。  默认false -->  
      <setting name="mapUnderscoreToCamelCase" value="false"/>  
      <!-- MyBatis 利用本地缓存机制（Local Cache）防止循环引用（circular references）和加速重复嵌套查询。  
             默认值为 SESSION，这种情况下会缓存一个会话中执行的所有查询。  
            若设置值为 STATEMENT，本地会话仅用在语句执行上，对相同 SqlSession 的不同调用将不会共享数据。  -->  
      <setting name="localCacheScope" value="SESSION"/>  
      <!-- 当没有为参数提供特定的 JDBC 类型时，为空值指定 JDBC 类型。 某些驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER。  -->  
      <setting name="jdbcTypeForNull" value="OTHER"/>  
    <!--   指定哪个对象的方法触发一次延迟加载。  -->  
      <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>  
    </settings> 


```

#### 事务
MyBatis 事务管理类型：
* JDBC - 这个类型直接全部使用 JDBC 的提交和回滚功能。它依靠使用连接的数据源来管理事务的作用域。
* MANAGED - 这个类型什么不做 ， 它从不提交 、 回滚和关闭连接 。 而是让窗口来管理事务的全部生命周期 。（比如说 Spring 或者 JAVAEE 服务器）

数据源类型有三种： UNPOOLED ， POOLED ， JNDI 。
* UNPOOLED - 这个数据源实现只是在每次请求的时候简单的打开和关闭一个连接。虽然这有点慢，但作为一些不需要性能和立即响应的简单应用来说,不失为一种好选择 。 
* POOLED - 这个数据源缓存 JDBC 连接对象用于避免每次都要连接和生成连接实例而需要的验证时间 。对于并发 WEB 应用，这种方式非常流行因为它有最快的响应时间。
* JNDI - 这个数据源实现是为了准备和 Spring 或应用服务一起使用，可以在外部也可以在内部配置这个数据源，然后在 JNDI 上下文中引用它。

#### SQL映射文件
 1. 使用相对路径
    <mappers> 
        <mapper resource="org/mybatis/builder/UserMapper.xml"/>
    </mappers> 
 2. 使用全路径
    <mappers> 
		<mapper url="file:///var/sqlmaps/AuthorMapper.xml"/>
    </mappers> 


### Mapper.xml配置
SQL 映射文件结构
* cache -  配置给定命名空间的缓存。 
* cache-ref –  从其他命名空间引用缓存配置。 
* resultMap  –  最复杂，也是最有力量的元素，用来描述如何从数据库结果集中来加载对象
* sql –  可以重用的 SQL 块，也可以被其他语句引用。  
* insert –  映射插入语句 (主键策略)
* update –  映射更新语句  
* delete –  映射删除语句 
* select –  映射查询语句

### 动态 SQL
* if  	
* choose(when,otherwise) 
* trim(where,set) 
* forea
* ch

### 深入
1. HandlerType
2. 拦截器
3. 事务
4. 插件

### tips
* 区分 #{} 和 ${}的不同应用场景
* 延迟加载
* Spring环境中使用mybatis-spring而不直接使用mybatis原生接口
