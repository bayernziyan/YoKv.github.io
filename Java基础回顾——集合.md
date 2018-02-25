---
title: 集合
date: 2017-08-23 18:57:17
categories: 
- Java基础
---

## 集合综述
Java集合框架主要包括两种，一种是Collection，另一种是Map。Collection接口又有3种子类型，List、Set和Queue。

<!--more-->

[集合框架图](http://upload-images.jianshu.io/upload_images/2243690-9cd9c896e0d512ed.gif?imageMogr2/auto-orient/strip)

## Collection

### List
#### AbstractList
AbstractList 继承自 AbstractCollection 抽象类，实现了 List 接口 ，是 ArrayList 和 AbstractSequentiaList 的父类。
AbstractList 作为 List 家族的中坚力量，制定了List实现的模板，具有以下特征:
* 实现了 List 的期望
* 继承了 AbstractCollection 的传统
* 创建了内部的迭代器 Itr, ListItr
* 有两个内部子类 SubList 和 RandomAccessSublist

##### AbstractSequentialList
AbstractSequentialList 继承自 AbstractList的抽象类，是 LinkedList 的父类，是 List 接口 的简化版实现。 AbstractSequentialList 只支持按次序访问，而不像 AbstractList 那样支持随机访问。
想要实现一个支持按次序访问的 List的话，只需要继承这个抽象类，然后把指定的抽象方法实现就好了。需要实现的方法：size(),listIterator()

#### ArrayList
ArrayList是List接口的动态数组的实现。在ArrayList中，我们即可以通过元素的序号快速获取元素对象；这就是快速随机访问(实现了RandomAccess接口)。

#### LinkedList
LinkedList 是一个继承于AbstractSequentialList的双向链表。它也可以被当作堆栈、队列或双端队列进行操作。
LinkedList 实现 List 接口，能对它进行队列操作。
LinkedList 实现 Deque 接口，即能将LinkedList当作双端队列使用。
LinkedList 实现了Cloneable接口，即覆盖了函数clone()，能克隆。
LinkedList 实现java.io.Serializable接口，这意味着LinkedList支持序列化，能通过序列化去传输。
LinkedList 是非同步的。

LinkedList的本质是双向链表。
* LinkedList继承于AbstractSequentialList，并且实现了Dequeue接口。 
* LinkedList包含两个重要的成员：header 和 size。
header是双向链表的表头，它是双向链表节点所对应的类Entry的实例。Entry中包含成员变量： previous, next, element。其中，previous是该节点的上一个节点，next是该节点的下一个节点，element是该节点所包含的值。size是双向链表中节点的个数。

* LinkedList 实际上是通过双向链表去实现的。
它包含一个非常重要的内部类：Entry。Entry是双向链表节点所对应的数据结构，它包括的属性有：当前节点所包含的值，上一个节点，下一个节点。
* 从LinkedList的实现方式中可以发现，它不存在LinkedList容量不足的问题。
* LinkedList的克隆函数，即是将全部元素克隆到一个新的LinkedList对象中。
* LinkedList实现java.io.Serializable。当写入到输出流时，先写入“容量”，再依次写入“每一个节点保护的值”；当读出输入流时，先读取“容量”，再依次读取“每一个元素”。
*  由于LinkedList实现了Deque，而Deque接口定义了在双端队列两端访问元素的方法。提供插入、移除和检查元素的方法。每种方法都存在两种形式：一种形式在操作失败时抛出异常，另一种形式返回一个特殊值（null 或 false，具体取决于操作）。

#### Vector
Vector是AbstractListt一个实现类，完全支持List的全部功能，Vector类也封装了一个动态的，允许在分配的Object[]数组，Vector是一个比较古老的集合，JDK1.0就已经存在，建议尽量不要使用这个集合，Vector与ArrayList的主要是区别是，Vector是线程安全的，但是性能比ArrayList要低。
子类：Stack
每个方法中都添加了synchronized的关键字来保证同步，所以它是线程安全

### Set
一个不包括重复元素（包括可变对象）的Collection，是一种无序的集合。Set不包含满 a.equals(b) 的元素对a和b，并且最多有一个null。

#### AbstractSet
此类提供 Set 接口的骨干实现，从而最大限度地减少了实现此接口所需的工作。

通过扩展此类来实现一个 set 的过程与通过扩展 AbstractCollection 来实现 Collection 的过程是相同的，除了此类的子类中的所有方法和构造方法都必须服从 Set 接口所强加的额外限制（例如，add 方法必须不允许将一个对象的多个实例添加到一个 set 中）。
注意，此类并没有重写 AbstractCollection 类中的任何实现。它仅仅添加了 equals 和 hashCode 的实现。

#### TreeSet
TreeSet继承了AbstractSet，是基于TreeMap实现的。TreeMap是一个有序的二叉树，那么同理TreeSet同样也是一个有序的，它的作用是提供有序的Set集合。

#### HashSet
HashSet继承了AbstractSet
对于HashSet而言，它是基于HashMap实现的，HashSet底层使用HashMap来保存所有元素，因此HashSet 的实现比较简单，相关HashSet的操作，基本上都是直接调用底层HashMap的相关方法来完成

#### LinkedHashSet

* 维护着一个运行于所有条目的双重链接列表。此链接列表定义了迭代顺序，该迭代顺序可为插入顺序或是访问顺序。
* LinkedHashSet 继承与 HashSet，并且其内部是通过 LinkedHashMap 来实现的。
* 如果我们需要迭代的顺序为插入顺序或者访问顺序，那么 LinkedHashSet 是需要你首先考虑的

#### SortedSet
 SortedSet是个接口，它里面的（只有TreeSet这一个实现可用）中的元素一定是有序的。

#### EnumSet
EnumSet 是一个与枚举类型一起使用的专用 Set 实现。枚举set中所有元素都必须来自单个枚举类型（即必须是同类型，且该类型是Enum的子类）。

### Queue

#### AbstractQueue

#### ArrayDeque

#### Deque
##### LinkedList

#### PriorityQueue

### Collections
Collections封装了大量集合操作

## Map
[参考](http://blog.csdn.net/jzhf2012/article/details/8540670)
### TreeMap
TreeMap实现SortedMap接口，能够把它保存的记录根据键排序，默认是按键值的升序排序，也可以指定排序的比较器，当用Iterator遍历TreeMap时，得到的记录是排过序的。如果使用排序的映射，建议使用TreeMap。在使用TreeMap时，key必须实现Comparable接口或者在构造TreeMap传入自定义的Comparator，否则会在运行时抛出java.lang.ClassCastException类型的异常。

### HashMap
从结构实现来讲，HashMap是数组+链表+红黑树（JDK1.8增加了红黑树部分）实现。它根据键的hashCode值存储数据，大多数情况下可以直接定位到它的值，因而具有很快的访问速度，但遍历顺序却是不确定的。 HashMap最多只允许一条记录的键为null，允许多条记录的值为null。HashMap非线程安全，即任一时刻可以有多个线程同时写HashMap，可能会导致数据的不一致。如果需要满足线程安全，可以用 Collections的synchronizedMap方法使HashMap具有线程安全的能力，或者使用ConcurrentHashMap。

### Hashtable
Hashtable是遗留类，很多映射的常用功能与HashMap类似，不同的是它承自Dictionary类，并且是线程安全的，任一时间只有一个线程能写Hashtable，并发性不如ConcurrentHashMap，因为ConcurrentHashMap引入了分段锁。Hashtable不建议在新代码中使用，不需要线程安全的场合可以用HashMap替换，需要线程安全的场合可以用ConcurrentHashMap替换。

### LinkedHashMap
LinkedHashMap是HashMap的一个子类，保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录肯定是先插入的，也可以在构造时带参数，按照访问次序排序。
