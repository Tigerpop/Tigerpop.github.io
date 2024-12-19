---
layout: posts
title: "sqlite3_study2"
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

def convert(value):
    if value.startswith('~'):
        return value.strip('~')  # 移除str首尾的 ～。
    if not value:  # null
        value = 0
    return (value)

conn = sqlite3.connect('USA_food.db')
curs = conn.cursor()

# 用游标去执行一个数据库建table操作。

curs.execute('''
CREATE TABLE food(
id TEXT,
desc TEXT,
water TEXT,
kcal TEXT,
protein TEXT,
fat TEXT,
ash TEXT,
carbs TEXT,
fiber TEXT,
sugar TEXT,
ddd  TEXT,
sss TEXT,
huanhang TEXT
)''')

query = 'INSERT INTO food VALUES(?,?,?,?,?,?,?,?,?,?,?,?,?)'
field_count = 13

# i = 0

# for line in open('./sr28asc/FOOD_DES.txt'):

#     print(line)

#     fields = line.split('^')

#     print(fields)  # 14colomn

#     print(type(fields)) # list

#     i += 1

#     print(i)

for line in open('./sr28asc/FOOD_DES.txt'):
    fields = line.split('^')
    vals = [convert(f) for f in fields[:field_count]] # 切片去掉最后一个\n 换行符。
    # print(type(vals))
    curs.execute(query,vals)  # 执行插入操作，插入内容为vals 的list.

conn.commit()
conn.close()




```

