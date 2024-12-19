---
layout: posts
title: "shelf_database"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
# coding:utf-8

import sys,shelve

def enter_command():
    cmd = input("enter command （如需帮助请输入 '?'）")
    cmd = cmd.strip().lower()
    return cmd

def print_help():
    print('commands are :\n',
          ' store: store information about a person\n',
          'lookup: looks up a person from id number\n',
          'quit: save changes and exit\n'
          )

def store_person(db):
    pid = input('请输入id')
    person = {}
    person['name'] = input('name')
    person['age'] = input('age')
    person['phone'] = input('phone')

    db[pid] = person

def lookup_person(db):
    try:
        pid = input(' 请输入id ')
        field = input('输入你想要查询的属性 ')
        field = field.strip()
        print(field.capitalize() + '  ',db[pid][field])
    except:
        print('您的操作有误，或者没有此人。请重试。')

def main():
    database = shelve.open('/Users/chenyushao/Desktop/python_draft/python基础知识第3版/batabase.bat')
    # batabase 类似于dict.
    try:
        while True:
            cmd = enter_command()
            if cmd == 'store':
                store_person(database)
            elif cmd == 'lookup':
                lookup_person(database)
            elif cmd == '?':
                print_help()
            elif cmd == 'quit':
                return
    finally:
        database.close()


if __name__ == '__main__':
    main()
```


