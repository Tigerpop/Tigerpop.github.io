---
layout: posts
title: "super"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
# coding:utf-8

class Parent():
    def __init__(self,p):
        print('hello i am parent %s' % p)

class Child(Parent):
    def __init__(self,c,p):
        print('hello i am %s' % c)
        super().__init__(p) # 用super调用父类

if __name__ == '__main__' :
    c = Child(c='child',p='parent')
```

