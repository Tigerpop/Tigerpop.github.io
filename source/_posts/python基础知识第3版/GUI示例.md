---
layout: posts
title: "GUI示例"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->
```python
# coding:utf-8

from tkinter import *

def clicked():
    print('I was clicked')

top = Tk() # 创建控件
btn = Button()
btn.pack()  # 布局管理器
btn['text'] = 'Click me!'
btn['command'] = clicked
btn.config(text='click me second',command=clicked) # 覆盖了之前的btn字典赋值。
Button(text='click me too',command=clicked).pack()

# mainloop()  # 调用mainloop循环进入Tkinter主事件循环，前面是把主事件安排好，最后再进入安排好的循环。

Label(text="I'am in the first window!").pack()
second = Toplevel() # 打开一个主窗口之外的顶级窗口。
Label(second,text="I'am in the second window!").pack()

# 默认的布局是把顶级主窗口作为主控件。

# label文本标签可以在第一个参数中指定主控件。

for i in range(10):
    Button(text=i).pack()

mainloop()


```

