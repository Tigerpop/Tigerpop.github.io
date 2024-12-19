---
layout: posts
title: "Hive SQL 数据操控、查询语言 DML、DQL"
date: 2022-10-29 15:04:10
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "hive"
tags:
- "hive"
- "sql"
---

hive sql <!--more-->
# Hive SQL 数据操控、查询语言。DML、DQL



![截屏2022-03-06 22.26.57](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2022.26.57-7026773.jpg)

![截屏2022-03-06 22.29.48](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2022.29.48.jpg)

hive推荐使用load加载数据，大数据用加载，insert会慢的怀疑人生。

![截屏2022-03-06 22.34.07](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2022.34.07.jpg)

![截屏2022-03-06 22.38.57](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2022.38.57.jpg)

![截屏2022-03-06 22.43.15](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2022.43.15.jpg)

重要：master主机的hiveserver2 运行，slave的beeline运行local指的也是master的本地。

![截屏2022-03-06 22.45.41](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2022.45.41.jpg)

![截屏2022-03-07 10.55.27](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2010.55.27.jpg)

![截屏2022-03-07 11.02.58](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2011.02.58.jpg)

![截屏2022-03-07 11.14.33](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2011.14.33.jpg)

![截屏2022-03-07 11.24.31](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2011.24.31.jpg)

![截屏2022-03-07 11.25.39](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2011.25.39.jpg)

![截屏2022-03-07 11.31.24](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2011.31.24.jpg)

![截屏2022-03-07 11.32.43](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2011.32.43.jpg)

![截屏2022-03-07 11.36.24](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2011.36.24.jpg)

![截屏2022-03-07 11.37.34](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2011.37.34.jpg)

![截屏2022-03-07 11.39.23](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2011.39.23.jpg)

![截屏2022-03-07 11.40.28](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2011.40.28.jpg)

![截屏2022-03-07 11.43.08](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2011.43.08.jpg)

![截屏2022-03-07 11.44.27](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2011.44.27.jpg)

![截屏2022-03-07 11.47.14](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2011.47.14.jpg)

![截屏2022-03-07 11.48.04](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2011.48.04.jpg)

![截屏2022-03-07 11.50.23](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2011.50.23.jpg)

​		注意：这个是overwrite！！！ 我之前就是一次不小心把hdfs的关键文件夹都给重写了，搞得全部重装一遍，一定要注意overwrite 别把关键文件夹都重写了！！！



![截屏2022-03-07 15.39.59](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2015.39.59.jpg)

![截屏2022-03-07 15.41.17](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2015.41.17.jpg)

![截屏2022-03-07 15.42.38](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2015.42.38.jpg)

![截屏2022-03-07 15.45.02](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2015.45.02.jpg)

![截屏2022-03-07 15.57.27](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2015.57.27.jpg)

![截屏2022-03-07 22.07.12](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2022.07.12.jpg)

![截屏2022-03-07 22.07.47](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2022.07.47.jpg)

![截屏2022-03-07 22.10.20](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2022.10.20.jpg)

![截屏2022-03-07 22.14.36](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2022.14.36.jpg)

![截屏2022-03-07 22.16.14](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2022.16.14.jpg)

​		事务表只能 insert 不能load ，这点有点坑。

![截屏2022-03-07 22.19.29](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2022.19.29.jpg)

![截屏2022-03-07 22.20.45](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2022.20.45.jpg)

​		只有事务表才能 update、delete，前文说过了。



## select 讲解

```sql
--step1:创建普通表t_usa_covid19
drop table if exists t_usa_covid19;
CREATE TABLE t_usa_covid19(
       count_date string,
       county string,
       state string,
       fips int,
       cases int,
       deaths int)
row format delimited fields terminated by ",";
--将源数据load加载到t_usa_covid19表对应的路径下
load data local inpath '/root/hivedata/us-covid19-counties.dat' into table t_usa_covid19;

select * from t_usa_covid19;

--step2:创建一张分区表 基于count_date日期,state州进行分区
CREATE TABLE if not exists t_usa_covid19_p(
     county string,
     fips int,
     cases int,
     deaths int)
partitioned by(count_date string,state string)
row format delimited fields terminated by ",";

--step3:使用动态分区插入将数据导入t_usa_covid19_p中
set hive.exec.dynamic.partition.mode = nonstrict;

insert into table t_usa_covid19_p partition (count_date,state)
select county,fips,cases,deaths,count_date,state from t_usa_covid19;


```

![截屏2022-03-07 22.40.12](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2022.40.12.jpg)

```sql
---------------Hive SQL select查询基础语法------------------

--1、select_expr
--查询所有字段或者指定字段
select * from t_usa_covid19_p;
select county, cases, deaths from t_usa_covid19_p;
--查询匹配正则表达式的所有字段
SET hive.support.quoted.identifiers = none; --反引号不在解释为其他含义，被解释为正则表达式
select `^c.*` from t_usa_covid19_p;
--查询当前数据库
select current_database(); --省去from关键字
--查询使用函数
select count(county) from t_usa_covid19_p;


--2、ALL DISTINCT
--返回所有匹配的行
select state from t_usa_covid19_p;
--相当于
select all state from t_usa_covid19_p;
--返回所有匹配的行 去除重复的结果
select distinct state from t_usa_covid19_p;
--多个字段distinct 整体去重
select  county,state from t_usa_covid19_p;
select distinct county,state from t_usa_covid19_p;
select distinct sex from student;
```

![截屏2022-03-07 22.45.45](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2022.45.45.jpg)

![截屏2022-03-07 22.53.05](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2022.53.05.jpg)

![截屏2022-03-07 22.55.23](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2022.55.23.jpg)

典型错误如下

![截屏2022-03-07 22.58.27](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2022.58.27.jpg)

```sql
--先where分组前过滤（此处是分区裁剪），再进行group by分组， 分组后每个分组结果集确定 再使用having过滤
select state,sum(deaths)
from t_usa_covid19_p
where count_date = "2021-01-28"
group by state
having sum(deaths) > 10000;

--这样写更好 即在group by的时候聚合函数已经作用得出结果 having直接引用结果过滤 不需要再单独计算一次了
select state,sum(deaths) as cnts
from t_usa_covid19_p
where count_date = "2021-01-28"
group by state
having cnts> 10000;
```

![截屏2022-03-07 23.00.10](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2023.00.10.jpg)

```bash
--返回结果集的前5条
select * from t_usa_covid19_p
where count_date = "2021-01-28"
  and state ="California"
limit 5;

--返回结果集从第1行开始 共3行
select * from t_usa_covid19_p
where count_date = "2021-01-28"
and state ="California"
limit 2,3; --注意 第一个参数偏移量是从0开始的
```

![截屏2022-03-07 23.03.15](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-07%2023.03.15.jpg)

![截屏2022-03-08 14.44.51](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2014.44.51.jpg)

![截屏2022-03-08 14.49.29](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2014.49.29.jpg)

![截屏2022-03-08 14.56.32](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2014.56.32.jpg)

![截屏2022-03-08 14.52.44](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2014.52.44.jpg)

![截屏2022-03-08 14.53.28](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2014.53.28.jpg)

​		hash函数对2取mod，分成 奇偶 两个组，且每组排了序。

![截屏2022-03-08 14.54.37](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2014.54.37.jpg)

​		我们可以知道cluster by 的局限性，cluster by 的使用只能 针对同一个字段分组，且每组内排序只能是正序。 比如目的是按sex 分组且 按age的降序排列；这样的要求cluster by 就是不能满足的，若先cluster by 再 order by 会报错，因为order by 是全局排序的。

​		为此我们引入 distribute by + sort by

![截屏2022-03-08 15.04.57](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2015.04.57.jpg)

![截屏2022-03-08 15.06.06](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2015.06.06.jpg)

![截屏2022-03-08 15.08.36](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2015.08.36.jpg)

![截屏2022-03-08 15.09.57](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2015.09.57.jpg)

![截屏2022-03-08 15.14.06](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2015.14.06.jpg)

![截屏2022-03-08 15.18.14](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2015.18.14.jpg)

![截屏2022-03-08 15.23.02](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2015.23.02.jpg)

![截屏2022-03-08 22.24.51](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2022.24.51.jpg)

```sql
-----------------Common Table Expressions（CTE）-----------------------------------
--select语句中的CTE
with q1 as (select num,name,age from student where num = 95002)
select *
from q1;

-- from风格
with q1 as (select num,name,age from student where num = 95002)
from q1
select *;

-- chaining CTEs 链式
with q1 as ( select * from student where num = 95002),
     q2 as ( select num,name,age from q1)
select * from (select num from q2) a;

-- union
with q1 as (select * from student where num = 95002),
     q2 as (select * from student where num = 95004)
select * from q1 union all select * from q2;

--视图，CTAS和插入语句中的CTE
-- insert
create table s1 like student;

with q1 as ( select * from student where num = 95002)
from q1
insert overwrite table s1
select *;

select * from s1;

-- ctas
create table s2 as
with q1 as ( select * from student where num = 95002)
select * from q1;

-- view
create view v1 as
with q1 as ( select * from student where num = 95002)
select * from q1;

select * from v1;
```

![截屏2022-03-08 22.37.13](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2022.37.13.jpg)

![截屏2022-03-09 00.16.39](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-09%2000.16.39.jpg)

内连接、左连接、右连接、全连接 都和mysql一样的。

补充几个

![截屏2022-03-08 23.49.53](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2023.49.53.jpg)

![截屏2022-03-08 23.56.42](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-08%2023.56.42.jpg)

```sql
-- A 不是内连接而是cross join ，因为后面是where，inner join 是on 作连接条件的。
-- B cross join
-- C inner join （虽然结果一样，但是内连接不会像cross join 一样先 生成两个表的笛卡尔积表，再根据where/on条件过滤。）
-- 无条件的 inner join 就是cross join

-- 注意：！！！！内连接可以看作是 笛卡尔积后，根据on做了筛选，保留了on后符合条件的行。
-- 注意：

​		就算是a和b表join+on仍然会有[笛卡尔积]的形式

​		因为如果a表和b表的id都有1对多的形式时，就是笛卡尔积

​		比如a中id=1的记录有3条（ID，维修时间），

​		b中id=1的记录有2条（ID，用户上报时间）

​		，join，on之后就会有6条！
-- 例子： a.id [1,2,3]  b.id[1,2,2,3]
-- a join b on a.id=b.id;
-- 先 a 和 b 做笛卡尔积得到 1-1 1-2 1-2 1-3，2-1 2-2 2-2 2-3，3-1 3-2 3-2 3-3。
-- 再 根据on 后的条件做筛选 保留 1-1 2-2 2-2 3-3，有4条。因为a.id=2有一条 b.id=2有两条。

-- 例子：a.id [1,1,1]  b.id[1,1,1]
-- a join b on a.id=b.id;
-- 结果是9条，因为 a.id=1有三条 b.id=1有三条。
```

![截屏2022-03-09 00.20.41](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-09%2000.20.41.jpg)

![截屏2022-03-09 00.25.59](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-09%2000.25.59.jpg)

 		这个reducer流式传输地方没有太看懂。

​		在hive的连接实现中，它必须从多个表中获取记录，按连接键对它们进行排序，然后按正确的顺序将它们整理在一起。它必须读取按不同表分组的数据，因此它们必须看到来自不同表的组，一旦看到所有表，就开始处理它们。第一个表中的第一组需要缓冲（保存在内存中），因为在看到最后一个表之前无法处理它们。最后一个表可以流式传输（每一行在读取时都会被处理），因为其他表组都在内存中，连接可以启动。

![0](Hive%20SQL%20%E6%95%B0%E6%8D%AE%E6%93%8D%E6%8E%A7%E3%80%81%E6%9F%A5%E8%AF%A2%E8%AF%AD%E8%A8%80%20DML%E3%80%81DQL.assets/%E6%88%AA%E5%B1%8F2022-03-09%2000.32.58.jpg)

统计词频的 map - reduce 伪代码：

```python
mapper (filename, file-contents):
  for each word in file-contents:
    emit (word, 1)  # 遇到重复的word， 就把 values 的后面再追加一个 1，values初始是 [1]，遇到两次相同word ，values就变成  [1,1]；丰富出多个 键值对。
 
reducer (word, values):
  sum = 0
  for each value in values:
    sum = sum + value
  emit (word, sum) # 给出一个键值对 ：{word单词 : 出现次数的统计}
```

Hive中的Join可分为Common Join（Reduce阶段完成join）和Map Join（Map阶段完成join）

Map Join作用及原理

作用简单来说，在Map阶段进行join，而不是Common Join那样在Reduce阶段按照join列进行分发后在每个Reduce节点上进行join，一来省去Shuffle这个代价昂贵的阶段，二来不需要分发也就没有倾斜的问题。

