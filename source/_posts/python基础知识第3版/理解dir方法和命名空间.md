---
layout: posts
title: "理解dir方法和命名空间"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
import name_and_age

class Yourfather:
    pass
class Yourmother:
    our_doge = 'xiaogou'
    def __init__(self,name):
        self.__name = name
        print(self.__name)
    def talk(self):
        pass
print(__name__)


print(dir()) # 打印当前模块中（全局名称空间）的 所有属性、方法、类名、引入模块名。
print(dir(Yourmother)) # 打（印局部名称空间）属性方法类名。

mother = Yourmother('chen')
print(mother.__dir__())   # 实例化后 打印对象的 （局部名称空间）的属性、方法、类名。
print(type(name_and_age))
print(type(__builtins__))
```

