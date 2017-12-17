---
title: redis数据结构-链表
date: 2017-12-10 17:30:27
categories:
- redis
---

## redis 链表
C语言没有链表的实现，redis构建了自己的实现

### 链表结构
<!--more-->
链表节点
```
typedef struct  listNode{
	//前置节点
	struct listNode  *prev;
	//后置节点
	struct listNode *next;
	//节点的值	
	void *value;
}listNode;

```

链表
```
typedef struct  list{
	//表头节点
	 listNode *head;
	//表尾节点
	 listNode *tail;
	//链表所包含的节点数量
	unsigned long len;
	//节点值复制函数
	void *(*dup)(void *ptr);
	//节点值释放函数
	void *(*free)(void *ptr);
	//节点值对比函数
	int (*match)(void *ptr,void *key);
	
}list;
```
上述链表结构提供了表头指针head，表尾指针tail，以及链表长度计数器len,而dup,free,match成员用于实现多态链表所需的类型特定函数？？？


### 特性
#### 双端
双向链表
#### 无环
头尾节点都是对应指向null,不会成环
#### 带表头表尾指针

#### 带长度计数器

#### 多态
链表节点使用*void指针来保存节点值，并且可以通过list结构的dup,free,match3个属性为节点设置类型特定函数，所以链表可以保存不同类型的值

