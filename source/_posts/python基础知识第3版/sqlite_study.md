---
layout: posts
title: "sqlite_study"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
# coding:utf-8

import sqlite3
conn = sqlite3.connect('somedatabase.db')

cur = conn.cursor() # 从连接获取游标。

conn.commit() # 每次修改数据库后，都记得提交。

conn.close()


```

