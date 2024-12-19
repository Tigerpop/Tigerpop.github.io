---
layout: posts
title: "find_sender"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
# coding:utf-8

import fileinput , re

def find_sender():
    while True :
        try:
            pat = re.compile('From: ([^:]+?)\<.*\>$')  # 通配符后的重复运算符 +？ 变非贪婪模式，通配符匹配尽可能少的内容。
            n = input('查找发件人，请输入想要匹配文件名：')
            for line in fileinput.input(n):
                m = pat.match(line)  # 逐行匹配,匹配成功返回一个matchobject对象，为真。
                if m:
                    print('find the sinder is :',m.group(1))
                    print('您成功找出了发件人。')
                    return

        except:
            print('您输入的文件不存在，请重新输入：')


def find_email_adder():
    while True:
        try:
            n = input('查找邮件地址，请输入想要匹配的文件名：')
            pat = re.compile(r'[< ]([a-zA-Z\-\.]+@[a-zA-Z\-\.]+)[ >]')
            addersses = set()
            i = 0
            for line in fileinput.input(n):
                i += 1
                # print('***********: i is :%d'%i,line)
                if i == 1:
                    print('第一行是',line)
                    for adderss in pat.findall(line):
                        addersses.add(adderss)
            for adderss in sorted(addersses):   # 让每一次查看效果能够差不多。
                print(adderss)
            print('您成功找出了头邮件地址。')
            return
        except:
            print('您输入的文件不存在，请重新输入：5555555')

if __name__ == "__main__":
    num = input("输入1查找发件人，输入其他值查找头邮件地址。")
    if num == '1':
        find_sender()
    elif num != '1':
        find_email_adder()


```

