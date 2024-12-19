---
layout: posts
title: "random_"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
from random import *
from time import *

data1 = (2016,1,1,0,0,0,-1,-1,-1)
time1 = mktime(data1)
data2 = (2017,1,1,0,0,0,-1,-1,-1)
time2 = mktime(data1)

random_time = uniform(time1,time2)
print(random_time,type(random_time))

print(asctime(localtime(random_time)))

num = int(input('How many dice?'))
sides = int(input('How many sides per die?'))
sum = 0
for i in range(num):
    sum += randrange(sides) + 1
    print('The %sth all_result is'%(i+1),sum)
```

