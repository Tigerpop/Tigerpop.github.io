---
layout: posts
title: "test__str__"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python多进程和多线程"
---

简介 <!--more-->

```python
# coding='utf-8'

iter_obj = iter([1,2,3])

def _next(iter_obj):
    try:
        return next(iter_obj)
    except StopIteration:
        return None

print(iter([1,2,3]))

# print(next(iter_obj))

# print(_next(iter_obj))

# print(_next(iter_obj))

# print(_next(iter_obj))

for i in iter_obj:     #for 循环比next方法好，不容易报错
    print(i)

def make_iter():
    for i in range(10):
        yield i
iter_obj = make_iter()
print(type(iter_obj))

for i in iter_obj:
    print(i)
print('_______')
for i in iter_obj:
    print(i)

iter_obj = (i for i in range(10))
for i in iter_obj:
    print(i)
print("=======")

for i in iter_obj:
    print(i)






```

