---
layout: posts
title: "sqlite3_study3"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
# coding:utf-8

import sqlite3,sys

conn = sqlite3.connect('USA_food.db')
curs = conn.cursor()

query = 'SELECT * FROM food ' # + 'WHERE'+ sys.argv[1]

# print(query)

curs.execute(query)  # 用游标执行查询
print(curs.description)

names = [f[0] for f in curs.description]  # 等价于下方。

# names = []

# for f in curs.description:

#     names.append(f[0])

print(names)

# ('id', None, None, None, None, None, None), ('desc', None, None, None, None, None, None)

# ['id', 'desc', 'water', 'kcal', 'protein', 'fat', 'ash', 'carbs', 'fiber', 'sugar', 'ddd', 'sss', 'huanhang']

# fetchall 以序列的序列方式取回余下所有的行。

print(curs.fetchone())  # 取一行演示 ('01001', '0100', 'Butter, salted', 'BUTTER,WITH SALT', '', '', 'Y', '', '0', '', '6.38', '4.27', '8.79')
for row in curs.fetchall():
    for pair in zip(names,row):  # 两个列表打包成新列表，新列表元素是一个个组元。
        print('{}:{}'.format(*pair))  # 使用*在tuple或者list前，相当于把tuple或者list拆开使用。
    print()




```

