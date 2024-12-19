---
layout: posts
title: "re_"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
# coding:utf-8

import re
test_1 = re.match('i','python')
print(test_1)
test_2 = re.match('p','python') # 只匹配开头。
print(test_2)

some_text = 'alpha, beta,,,,gamma   delta'
test_3 = re.split('[, ]+', some_text)
print('some_text is :',some_text)
print('test_3 is :',test_3)
test_4 = re.split('o','fobar')
print('test_4 is :',test_4)
test_5 = re.split('o','fooobar')  # 重复的分割，留下n-1个空格被分割出来。['f', '', '', 'bar']
print('test_5 is :',test_5)
test_6 = re.split('o(o)','fooobar')
print('test_6 is :',test_6)

pat = '[a-zA-Z]+'
text = '"Hm...Err -- are you sure?" he said , sounding insecure.'
word = re.findall(pat,text)
print(word)

pat = r'[.?",\-]+'
Symbol = re.findall(pat,text)
print(Symbol)

pat = '{name}'
text = 'Dear {name} ...'
sub_ = re.sub(pat,'ChenYushao',text)
print(sub_)


```

