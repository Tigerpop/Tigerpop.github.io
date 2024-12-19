---
layout: posts
title: "GUI文本编辑器"
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
from tkinter.scrolledtext import ScrolledText

def load():
    with open(filename.get())as file:
        contents.delete('1.0',END)
        contents.insert(INSERT,file.read())

def save():
    with open(filename.get(),'w')as file:
        file.write(contents.get('1.0',END))

top = Tk()
top.title('Simple Editor')

contents = ScrolledText()
contents.pack(side=BOTTOM,expand=True,fill=BOTH)
contents_2 = ScrolledText()    # constens_2 没有关联上方法，所以没有用
contents_2.pack(side=BOTTOM,expand=True,fill=BOTH)

filename = Entry()
filename.pack(side=LEFT,expand=True,fill=BOTH)
filename_2 = Entry()   # filename_2 没有关联上方法，所以没有用
filename_2.pack(side=LEFT,expand=True,fill=BOTH)

Button(text='Open',command=load).pack(side=LEFT)
Button(text='Save',command=save).pack(side=LEFT)

mainloop()
```

