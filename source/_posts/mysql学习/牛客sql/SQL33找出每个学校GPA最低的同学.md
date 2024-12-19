---
layout: posts
title: SQL33找出每个学校GPA最低的同学
date: 2022-11-25 20:32:29
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "mysql"
tags:
- "窗口函数"
---



```sql
# 一看见不方便的 group 首先想到 窗口函数。
# 写的时候，一步一步写，做好检查。
select device_id,university,gpa 
from
    (select 
        device_id,
        university,
        gpa,
        row_number() over(partition by university order by gpa ) as rk
    from user_profile)
    as table1
where rk=1;

# # 先写下面这个窗口，检查以下长什么样子。
# select 
#     university,
#     gpa,
#     row_number() over(partition by university order by gpa ) as rk
# from user_profile;

```

