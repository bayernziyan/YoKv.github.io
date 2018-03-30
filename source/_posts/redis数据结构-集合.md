---
title: redis数据结构-集合
date: 2017-12-17 17:30:27
categories:
- redis
---

## redis 整数集合
整数集合是集合键的底层实现之一，当一个集合只包含整数元素，并且这个集合的元素数量不多时，redis使用整数集合作为集合键的底层实现之一。

### 集合实现
<!--more-->
```
typedef struct intset{
	//编码方式
	uint32_t encoding;
	//集合包含的元素数量
	uint32_t length;
	//保存元素的数组
	int8_t contents[];
}intset


```




## 使用场景
* 有序集合键的底层实现之一
