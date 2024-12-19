---
layout: posts
title: "python抽象类"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
# coding:utf-8

# python要使用抽象类，要父类继承ABC，有abstractmethod修饰器修饰的方法

# 凡事父类的抽象方法，在子类中都必须被继承。

# 同时 抽象类不能被实例化。

from abc import ABC, abstractmethod

class People(ABC):

    @abstractmethod
    def walk(self):
        pass
    
    @abstractmethod
    def eat(self,a):
        print(' i am your father ,eat %s.' %a)
    
    def dance(self):
        print('我在跳舞')

class Kingname(People):
    def __init__(self):
        pass
    def walk(self):
        print('i am a boy')
    def eat(self):
        super().eat('apple')

kingname = Kingname()
kingname.eat()
```

