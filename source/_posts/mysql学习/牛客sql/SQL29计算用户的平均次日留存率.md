---
layout: posts
title: SQL29计算用户的平均次日留存率
date: 2022-11-25 20:30:59
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "mysql"
tags:
- "窗口函数"
---


```sql
# 明确 第二天 还来的概率怎么求，第二天来了标记1，第二天没来标记0，对第二天的标记求平均值，就是第二天来的概率。
# 用到 窗口函数  加一个 向上平移方法lead。把第二次来的日期 向上平移一位 和 第一次来的日期对齐，可以比较出第二天是不是来了。和hive学习中连续几日访问的问题类似。
# 这样嵌套的查询，从里面写起。边写可以边输出检查。

select avg(if(datediff(date2,date1)=1,1,0))as avg_ret
from                                                                  # 日期是  前 减 后;
(select 
    date date1, 
    lead(date,1,0) over(partition by device_id order by date ) as date2  # 组内排序,向下是增大。
from 
    (select 
        distinct(device_id),
        date 
    from question_practice_detail
    )as table_distinct
)as table1;

# 通过以下这步，我们可以看见，表中有大量，一个设备同一天来了多次的情况，所以我们要去重。
# select 
# device_id,
# date date1, 
# lead(date,1,0) over(partition by device_id order by date ) as date2     # 组内排序,向下是增大。
# from question_practice_detail;
# 
# mysql> select datediff('2021-08-09','2021-08-10');
# +-------------------------------------+
# | datediff('2021-08-09','2021-08-10') |
# +-------------------------------------+
# |                                  -1 |
# +-------------------------------------+
# 1 row in set (0.01 sec)

# mysql> select datediff('2021-08-10','2021-08-09');
# +-------------------------------------+
# | datediff('2021-08-10','2021-08-09') |
# +-------------------------------------+
# |                                   1 |
# +-------------------------------------+
# 1 row in set (0.00 sec)
# 
```

