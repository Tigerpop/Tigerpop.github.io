---
layout: posts
title: "exec_eval_test"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
# coding:utf-8

dict_ = {}

a_ = "test = '我是你爸爸！孙子。'"
a = '1+3'
b = 'test'

print(str(eval(a,dict_)))
exec(a_,dict_)
print(str(eval(b,dict_)))


```

