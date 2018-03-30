---
title: redis数据结构-跳跃表
date: 2017-12-17 17:30:27
categories:
- redis
---

## redis 跳跃表(zcard)
跳跃表是一种有序数据结构，它通过在每个节点维持多个指向其他节点的指针，从而达到快速访问节点的目的。跳跃表效率能和平衡树媲美。redis底层使用跳跃表作为```有序集合键```的底层实现之一，如果有序集合包含的元素数量较多，或者有序集合中元素成员是比较长的字符串，redis就使用跳跃表作为有序集合键的实现。跳跃表在redis中只在两个地方使用，其一：实现有序集合；其二：在集群节点中用作数据结构。

### 跳跃表实现
<!--more-->

#### 跳跃表节点
```
typedef  struct zskiplistNode{
	//后退指针
	struct zskiplistNode *backNode;
	//分值
	double score;
	//成员对象
	robj *obj;
	//层
	struct zskiplistLevel{
		//前进指针
		struct zskiplistNode *forward;
		//跨度
		unsigned int span;
	} level[];
}zskiplistNode


```

#### 跳跃表
```
typedef  struct zskiplist{
	//表头节点和表尾节点
	struct zskiplistNode *header,*tail;
	//表节点数量
	unsigned long length;
	//表中层数最大的节点的层数
	int level;
}zskiplist


```



## 使用场景
* 有序集合键的底层实现之一
* 集群节点中用作内部数据结构

