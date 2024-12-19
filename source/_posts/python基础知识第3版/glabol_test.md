---
layout: posts
title: "glabol_test"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
a = 12
b = [1,2]
def test():
    try:
        # global a
        a += 1
        print(a)
    except:
        pass
    print(b,' ',*b) # python里 局部用全局变量 如果是list和dict 可以免global声明。

test()

class Person():
    def __init__(self,name,age=None):
        self.name = name
        self.age = age
        print(' __Init__ was where')

    def run(self):
        print(f'{self.name}在奔跑')
    def jump(self):
        print(f'{self.name}在跳跃')
    def work(self):
        self.run()
        self.jump()
        def sleep(name):  # 在 类的函数中 再定义函数，可以不用self，一样被此函数内部调用。
            return name
        result = sleep(self.name)
        print('sleep result is',result)
    
    def sleep_1():     # 类中的函数 没有添加self作为参数 就不能被调用。
        print('sleep_1 ')


xiaomu = Person(name='xioamu',age= 18)
xiaomu.name= 'xiaomu_11'
xiaomu.jump()
dewei = Person(name= 'chen')
dewei.jump()

dewei.top = 174
print(dewei.top)

# print(xiaomu.top)

print(dewei.age)
print('~~~~~~~~~~~~~')

xiaomu.work()

# xiaomu.sleep()
```

