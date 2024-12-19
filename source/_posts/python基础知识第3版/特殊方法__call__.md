---
layout: posts
title: "特殊方法__call__"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
# coding:utf8

'''
Python中那些能够在后面加()来调用执行的对象，被称为可调用对象。可调用对象包括自定义函数、Python内置函数、实例对象和实例方法等。

call()方法是Python中一个很特殊的方法。凡是可调用对象，都可以通过调用__call__()方法来调用该对象。如果类中定义了__call__()方法，那么该类的实例对象也将成为可调用对象。该对象被调用时，将执行__call__()方法中的代码。

下面我们分别对这几种可调用对象执行__call__()方法。

把原本不可以被调用的类，变得也可以调用了，而且内置的__call__()会运行。
'''
def test():
    print("Function test() is called")

print("Function test() is callable: %s" % callable(test))
test()
test.__call__()

# Function test() is callable: True

# Function test() is called

# Function test() is called

print("Build-in function int() is callable: %s" % callable(int))
print(int(3))
print(int.__call__(3))

# Build-in function int() is callable: True

# 3

# 3

class Person(object):
    pass

c = Person()
print("Object c is callable: %s" % callable(c))

# Object c is callable: False

class Person(object):

    def __call__(self):
        print("Method __call__() is called")

d = Person()
print("Object d is callable: %s" % callable(d))
d()

# Object d is callable: True

# Method __call__() is called


```

