---
title: redis数据结构-字典
date: 2017-12-11 17:30:27
categories:
- redis
---

## redis 字典
键值对结构，C语言没有内置的实现。redis的数据库由map来实现的，数据库的CRUD也是在字典的操作之上的。字典还是哈希键的底层实现之一，当哈希键的键值对较多，又或者键值对中的元素都是比较长的字符串，redis会用字典作为哈希键的底层实现。

### 字典实现
<!--more-->
字典是以哈希表作为底层实现。

#### 哈希表
```
typedef struct  dictht{
	//哈希表数组
	dictEntry  **table;
	//哈希表大小
	unsigned long size;
	//哈希表大小掩码，用于计算索引值
	//总是等于size-1
	unsigned long sizemask;
	//哈希表已有的节点数
	unsigned long used;
}dictht;

```

#### 哈希表节点
```
typedef struct  dictEntry{
	//键
	void *key;
	//值
	union{
		void *val;
		unit64_t u64;
		int64_t s64;
	}v;
	//指向下个哈希表节点，形成链表
	struct dictEntry *next;
}dictEntry

```
key是键值对的键，v保存着键值对中的值，值可以是一个指针，一个uint64_t整数，或者是int64_t整数。
next指向另一个哈希表节点的指针，将多个哈希值相同的键值对连接在一起，解决哈希冲突问题。



#### 字典

```
typeof struct dict{
	//类型特定函数
	dictType *type;
	//私有数据
	void *privdata;	
	//哈希表
	dictht hb[2];
	//rehash索引，当rehash不在进行时为-1
	int rehashidx;
}dict

```

```
typeof struct dictType{
	//计算hash值的函数
	unsigned int (*hashFunction)(const void *key);
	//复制键的函数
	void *(*keyDup)(void *privdata,const void *key);	
	//复制值的函数
	void *(*valDup)(void *privdata,const void *obj);
	//对比键的函数
	int (*keyCompare)(void *privdata,const void *key1,const void *key2);
	//销毁键的函数
	void (*keyDestructor)(void *privdata,const void *key);	
	//销毁值的函数
	void (*keyDestructor)(void *privdata,const void *obj);	
}dictType

```
ht属性一般只有一个值，只有rehash时才会用到ht[1]，rehash时rehashidx记录进度，未rehash时为-1。

#### hash算法

要将一个新的键值对放入字典过程：
根据键计算hash值和索引值-->根据索引值将哈希表节点放到哈希表数组的指定索引上

##### 计算hash值和索引值

```
hash = dict->type->hashFunction(key);
index = hash & dict ->ht[x].sizemask;
```

##### 解决键冲突
多个键被分配到同一个索引上时发生冲突。
redis使用链地址法处理冲突，在dictEntry的next与下一个哈希表节点连接

##### rehash
当哈希表存储的键值对数量太多或太少时，为了让哈希表的负载因子维持在一个合理的范围内，需要对哈希表的大小进行扩展或收缩。

扩展或收缩哈希表通过rehash操作完成。
过程：
1. 为字典的ht[1]哈希表分配空间，这个hash表的空间大小取决于要执行的操作，以及ht[0]当前包含的键值对数量。
2. 将保存在ht[0]中所有键值对rehash到ht[1]中：rehash指重新计算键的hash值和索引值，然后讲键值对放置到ht[1]哈希表上指定位置。
3. 当ht[0]所包含的所有键值对都迁移到了ht[1]之后，释放ht[0]，将ht[1]设置为ht[1]，并在ht[1]新创建一个空白哈希表，为下一次rehash做准备。

##### 渐进式rehash
将保存在ht[0]中所有键值对rehash到ht[1]中不是一次性地，集中式地完成的，而是分多次，渐进式地完成的。
过程：
1. 为ht[1]分配空间，让字典同时拥有两个哈希表。
2. 在字典中维持一个索引计数器变量rehashidx，将他设置为0，表示正在rehash。
3. 在rehash过程中，每次对字典执行添加，删除，查找或者更新操作时，还会顺带将ht[0]哈希表在rehashidx索引上的所有键值对rehash到ht[1]上，当rehash工作完成时，程序将rehashidx属性的值+1.
4. 随着字典操作不断执行，最终在某个时间点上，ht[0]所有键值对都会被rehash到ht[1]上，这时程序将rehashidx属性的值设置为-1，表示rehash完成。

渐进式的rehash好处在于采取分而治之的方式，将计算工作均摊到了对字典的每个添加，删除，更新和查找的操作上，避免了集中式的计算带来的瞬间压力。
