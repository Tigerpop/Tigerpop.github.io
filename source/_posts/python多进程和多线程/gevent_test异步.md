---
layout: posts
title: "gevent_test异步"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python多进程和多线程"
---

简介 <!--more-->

```python
# coding:utf-8

import time,random
import asyncio,os
import gevent

def gevent_a():
    for i in range(5):
        print(i,'a gevent',os.getpid())
        gevent.sleep(random.random()*2)
    return 'gevent a result'

def gevent_b():
    for i in range(5):
        print(i,'b gevent ',os.getpid())
        gevent.sleep(random.random()*2)
    return 'genvent b result'
#

# async def a():

#     for i in range(4):

#         print(i,'a',os.getpid())

#         # time.sleep(random.random()*2)  # 如果使用time的sleep 是CPU级别的阻塞。

#         await asyncio.sleep(random.random()*2) # 使用异步就要所有环境都使用上异步。

#     return 'a function'

#

# async def b():

#     for i in range(4):

#         print(i,'b',os.getpid())

#         await asyncio.sleep(random.random() * 2)

#     return 'b function'

#

# async def main():

#     result = await asyncio.gather(

#         a(),

#         b()

#     )

#     #print(result) # 这里result就打印的是列表。

#     print(result[0],result[1]) # 按gather内的0，1索引打印列表内的值。

if __name__=='__main__':
    start = time.time()
    g_a = gevent.spawn(gevent_a)
    g_b = gevent.spawn(gevent_b)
    gevent_list = [g_a,g_b]
    result = gevent.joinall(gevent_list)
    print('result is : ',result )
    print(dir(result[0]))
    print(result[0].value)   # 异步有返回值。
    # asyncio.run(main())
    print('运行时间是： ',time.time() - start)  # 不用异步是6秒左右。异步后4秒。
    print('parant is %s' % os.getpid())


```

