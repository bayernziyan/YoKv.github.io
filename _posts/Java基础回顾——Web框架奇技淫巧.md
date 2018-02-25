---
title: Web框架奇技淫巧
date: 2017-09-03 20:53:54
categories: 
- Java基础
---

## Spring框架
<!--more-->

### 非controller层获取request
```
  RequestAttributes ra = RequestContextHolder.getRequestAttributes();
  ServletRequestAttributes sra = (ServletRequestAttributes) ra;
  HttpServletRequest request = sra.getRequest();

```
 