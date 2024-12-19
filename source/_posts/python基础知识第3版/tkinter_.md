---
layout: posts
title: "tkinter_"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
# coding:utf-8

import tkinter as tk
from tkinter import *
from tkinter.scrolledtext import ScrolledText

# top = Tk()

# mainloop()  # 进入tkinter主事件循环。

# def clicked():

#     print('I was clicked ')

# #

# # btn = Button()

# # btn.pack() # 使用布局管理器。

# # btn['text'] = '点击此处'

# # btn['command'] = clicked()

# # btn.config(text = 'Click me',command = clicked)   # 会覆盖掉之前 '点击此处' 那个btn.

# # Button(text='click me too ',command=clicked).pack()

# # Button(text='click me too too',command=clicked).pack()

#

# Label(text="I'm in the first window ").pack()

# second = Toplevel()

# Label(second,text="I'm zhe second window ").pack()

#

# for i in range(10):

#     Button(text=i).pack()  # 默认在第一个主控窗口中添加。

#

# top = Tk()

# def callback(event):

#     print(event.x,event.y)

#

# top.bind('<Button-1>',callback)

# 一下是简单GUI文本编辑器

def load():
    with open(filename.get()) as file:
        contents.delete('1',END)
        contents.insert(INSERT,file.read())

def save():
    with open(filename.get())as file:
        file.write(contents.get('1',END))

top = Tk()
top.title('simple editor')

contents = ScrolledText()    # 多行滚动文本.
contents.pack(side=BOTTOM,expand=True,fill=BOTH) # 布局这个多行文本的大框框在主页面的底部。

filename = Entry()   # 单行文本。
filename.pack(side=LEFT,expand=True,fill=X) # 布局这个单行文本的大框框在主页面的左侧，因为先设定了底部，所以单行文本在左上方。

Button(text='Open',command=load).pack(side=LEFT)# 因为 下方和左侧都有优先的布局，这个左侧布局就一定是在左上右边的位置，排队靠左排。
Button(text='Save',command=save).pack(side=LEFT)# 排队靠左排。 

mainloop()
```

