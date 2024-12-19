---
layout: posts
title: "read_write_test"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
# coding:utf-8

import io
f = open(r'./somefile.txt')
print(f.read(7))
print(f.read(4))
f.close()

f = open('./somefile.txt')
print(f.read())
f.close()

f = open('somefile.txt')
for i in range(3):
    print(str(i) + ':'+f.readline(),end='')
f.close()

import pprint
pprint.pprint(open('somefile.txt').readlines())


f = open('somefile.txt','w')
f.write('this\nis no\nhaiku')
f.close()

f = open('somefile.txt')
lines = f.readlines()
print(lines)
f.close()
lines[1] ="isn't a\n"  # 这里利用了 lines是可变对象的特性。
f = open('somefile.txt','w')
f.writelines(lines)
f.close()


```

