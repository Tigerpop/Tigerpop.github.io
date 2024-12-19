---
layout: posts
title: "filter_map_reduce"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python多进程和多线程"
---

简介 <!--more-->

```python
# coding:utf-8

from functools import reduce

res = filter(lambda x:x>1,[0,1,2])

print(type(res))  # 返回的是一个生成器

res = map(lambda x:x>1,[0,1,2])

print(type(res),list(res)) # 返回的是一个生成器

res = reduce(lambda x,y:x+y,[0,1,2])

print(res) # reduce返回的不是生成器，而是一个结果。

frunts = ['apple','banana','orange']
result = filter(lambda x:'e'in x,frunts)
print(list(result))
print(frunts)
print('--------')
def filter_func(item):
    if 'e' in item:
        return True
filter_result = filter(filter_func,frunts)
print(list(filter_result))

map_result = map(filter_func,frunts)
print(list(map_result))

reduce_result = reduce( lambda x,y: x*y, [1,2,3,4])
print((reduce_result))

reduce_result_str = reduce(lambda x,y:x+y,frunts)
print(reduce_result_str)




```

