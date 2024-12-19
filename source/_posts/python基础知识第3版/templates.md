---
layout: posts
title: "templates"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
# coding:utf-8

# 一个模版系统。

import fileinput ,re

field_pat = re.compile(r'\[(.+?)\]')    # 转为非贪婪模式。

scope = {} # 变量收集到这个字典中。

def replacement(match):
    code = match.group(1)
    try:
        print('******* eval match is :',match)
        end = str(eval(code,scope)) # 如果是表达式，用eval方法把表达式计算出来，scope来提供所需字典。
        print('********* eval scope dict is :',scope)
        # end = str(eval(code ))
        return end
    except SyntaxError:
        print('******* exec match is :',match)
        exec(code,scope)
        print('********* exec scope dict is :', scope)
        # exec(code)
        return ''   # 如果是赋值语句，就返回一个空字符串。

lines = []

for line in fileinput.input(inplace=False):

# filename = input('请输入 file_name： ')

# for line in fileinput.input(filename):

    lines.append(line)

text = ''.join(lines)
print('****** text is :     ',text)
print(field_pat.sub(replacement,text)) # 用field_pat匹配了text后，返回match，
                                       # 作为参数进入replacement，replacement的return值去替换text内容，
                                       # 也就是说sub方法的repl参数部分可以是一个字符串，也可以是一个函数，
                                       # 这个函数的输入是匹配好的match对象，要输出一个字符串去替换text内容。
```

