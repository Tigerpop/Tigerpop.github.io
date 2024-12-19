---
layout: posts
title: "heap_heap"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
# coding:utf-8

from heapq import *
from random import shuffle
data = list(range(10))
shuffle(data)
heap1 = []
for i in data:
    heappush(heap1, i)

print(heap1, type(heap1))
heapify(heap1)
print(type(heap1))

list_1 = [1,0.2,0.003,0.01,0.03]
heappush(list_1,1)
print(list_1)
heapify(list_1)
print(list_1)
```

