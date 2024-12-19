---
layout: posts
title: "time_"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
---

简介 <!--more-->

```python
import time

t = time.asctime()
print(t)
print(time.time())
print('localtime:',time.localtime(time.time()))
```

