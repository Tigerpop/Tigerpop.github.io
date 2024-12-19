---
layout: posts
title: "进程之间的通信process_queue"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python多进程和多线程"
tags:
- "进程"
---

简介 <!--more-->

```python
# coding:utf-8

import multiprocessing
import json,time

# 使用类来接收和发送消息,其实是用一个队列。

class Work(object):
    def __init__(self,q): # 在构造函数里定义一个队列对象q：要求传入一个队列 q，put 是进队列的方法。
        self.q = q
    def send(self,message):
        if not isinstance(message,str):  # 若不是字符串类型，这里有漏洞。
            message = json.dumps(message)  # 把message转为json格式存好。
        self.q.put(message)         # 进队列
    def send_all(self):
        for i in range(20):
            self.q.put(i)
            time.sleep(1)

    def receive(self):    # 不停的从队列中取
        while 1:
            result = self.q.get()
            try:
                res = json.loads(result)
            except:
                res = result
            print('recv is %s' % res)

if __name__=='__main__':
    q = multiprocessing.Queue()
    work = Work(q)
    send = multiprocessing.Process(target=work.send,args=({'name':111},))
    recv = multiprocessing.Process(target=work.receive)
    send_all_p = multiprocessing.Process(target=work.send_all)

    send_all_p.start()
    send.start()
    recv.start()
    
    # send.join()
    send_all_p.join()  #  把时间最长的进程阻塞掉就行了。
    
    recv.terminate()  # 手动终结无限循环的进程。


```

