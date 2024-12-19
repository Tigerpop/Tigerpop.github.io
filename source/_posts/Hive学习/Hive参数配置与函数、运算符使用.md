---
layout: posts
title: Hive参数配置与函数、运算符使用
date: 2022-10-29 15:14:33
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "hive"
tags:
- "hive"
---



简介 <!--more-->
# 配置属性

![截屏2022-03-09 15.54.49](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2015.54.49-7026880.jpg)

![截屏2022-03-09 15.55.12](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2015.55.12.jpg)

![截屏2022-03-09 16.01.59](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2016.01.59.jpg)

![截屏2022-03-09 16.05.23](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2016.05.23.jpg)

![截屏2022-03-09 16.12.59](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2016.12.59.jpg)

![截屏2022-03-09 16.19.15](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2016.19.15.jpg)

![截屏2022-03-09 16.20.40](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2016.20.40.jpg)

![截屏2022-03-09 16.23.50](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2016.23.50.jpg)

在这个会话中，把日志设置为debug，并输出在console控制台上。

![截屏2022-03-09 16.24.42](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2016.24.42.jpg)



# 运算符

![截屏2022-03-09 16.27.58](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2016.27.58.jpg)

![截屏2022-03-09 16.30.13](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2016.30.13.jpg)

![截屏2022-03-09 16.31.33](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2016.31.33.jpg)

```sql
----------------Hive中关系运算符--------------------------
--is null空值判断
select 1 from dual where 'itcast' is null;

--is not null 非空值判断
select 1 from dual where 'itcast' is not null;

--like比较： _表示任意单个字符 %表示任意数量字符
--否定比较： NOT A like B
select 1 from dual where 'itcast' like 'it_';
select 1 from dual where 'itcast' like 'it%';
select 1 from dual where  'itcast' not like 'hadoo_';
select 1 from dual where  not 'itcast' like 'hadoo_';

--rlike：确定字符串是否匹配正则表达式，是REGEXP_LIKE()的同义词。
select 1 from dual where 'itcast' rlike '^i.*t$';
select 1 from dual where '123456' rlike '^\\d+$';  --判断是否全为数字
select 1 from dual where '123456aa' rlike '^\\d+$';

--regexp：功能与rlike相同 用于判断字符串是否匹配正则表达式
select 1 from dual where 'itcast' regexp '^i.*t$';
```

![截屏2022-03-09 17.00.14](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2017.00.14.jpg)

![截屏2022-03-09 17.01.00](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2017.01.00.jpg)

![截屏2022-03-09 17.02.45](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2017.02.45.jpg)

```sql
Hive逻辑运算符
--与操作: A AND B   如果A和B均为TRUE，则为TRUE，否则为FALSE。如果A或B为NULL，则为NULL。
select 1 from dual where 3>1 and 2>1;
--或操作: A OR B   如果A或B或两者均为TRUE，则为TRUE，否则为FALSE。
select 1 from dual where 3>1 or 2!=2;
--非操作: NOT A 、!A   如果A为FALSE，则为TRUE；如果A为NULL，则为NULL。否则为FALSE。
select 1 from dual where not 2>1;
select 1 from dual where !2=1;
--在:A IN (val1, val2, ...)  如果A等于任何值，则为TRUE。
select 1 from dual where 11  in(11,22,33);
--不在:A NOT IN (val1, val2, ...) 如果A不等于任何值，则为TRUE
select 1 from dual where 11 not in(22,33,44);
--逻辑是否存在: [NOT] EXISTS (subquery)
--将主查询的数据，放到子查询中做条件验证，根据验证结果（TRUE 或 FALSE）来决定主查询的数据结果是否得以保留。
select A.* from A
where exists (select B.id from B where A.id = B.id);


--其他运算符
select 'it' || 'cast';
select concat()

select `array`(11,22,33)
from dual;
```

![截屏2022-03-09 17.24.31](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2017.24.31.jpg)

select concat ('a','b'); 



# function

![截屏2022-03-09 17.29.07](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2017.29.07.jpg)

![截屏2022-03-09 17.30.37](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2017.30.37.jpg)

![截屏2022-03-09 17.32.33](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2017.32.33.jpg)

![截屏2022-03-09 17.34.05](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2017.34.05.jpg)

```sql
------------String Functions 字符串函数------------
select concat("angela","baby");
--带分隔符字符串连接函数：concat_ws(separator, [string | array(string)]+)
select concat_ws('.', 'www', array('itcast', 'cn'));
--字符串截取函数：substr(str, pos[, len]) 或者  substring(str, pos[, len])
select substr("angelababy",-2); --pos是从1开始的索引，如果为负数则倒着数
select substr("angelababy",2,2);
--正则表达式替换函数：regexp_replace(str, regexp, rep)
select regexp_replace('100-200', '(\\d+)', 'num');
--正则表达式解析函数：regexp_extract(str, regexp[, idx]) 提取正则匹配到的指定组内容
select regexp_extract('100-200', '(\\d+)-(\\d+)', 2);
--URL解析函数：parse_url 注意要想一次解析出多个 可以使用parse_url_tuple这个UDTF函数
select parse_url('http://www.itcast.cn/path/p1.php?query=1', 'HOST');
--分割字符串函数: split(str, regex)
select split('apache hive', '\\s+');
--json解析函数：get_json_object(json_txt, path)
--$表示json对象
select get_json_object('[{"website":"www.itcast.cn","name":"allenwoon"}, {"website":"cloud.itcast.com","name":"carbondata 中文文档"}]', '$.[1].website');



--字符串长度函数：length(str | binary)
select length("angelababy");
--字符串反转函数：reverse
select reverse("angelababy");
--字符串连接函数：concat(str1, str2, ... strN)
--字符串转大写函数：upper,ucase
select upper("angelababy");
select ucase("angelababy");
--字符串转小写函数：lower,lcase
select lower("ANGELABABY");
select lcase("ANGELABABY");
--去空格函数：trim 去除左右两边的空格
select trim(" angelababy ");
--左边去空格函数：ltrim
select ltrim(" angelababy ");
--右边去空格函数：rtrim
select rtrim(" angelababy ");
--空格字符串函数：space(n) 返回指定个数空格
select space(4);
--重复字符串函数：repeat(str, n) 重复str字符串n次
select repeat("angela",2);
--首字符ascii函数：ascii
select ascii("angela");  --a对应ASCII 97
--左补足函数：lpad
select lpad('hi', 5, '??');  --???hi
select lpad('hi', 1, '??');  --h
--右补足函数：rpad
select rpad('hi', 5, '??');
--集合查找函数: find_in_set(str,str_array)
select find_in_set('a','abc,b,ab,c,def');
```

日期函数

```sql
----------- Date Functions 日期函数 -----------------
--获取当前日期: current_date
select current_date();
--获取当前时间戳: current_timestamp
--同一查询中对current_timestamp的所有调用均返回相同的值。
select current_timestamp();
--获取当前UNIX时间戳函数: unix_timestamp
select unix_timestamp();
--日期转UNIX时间戳函数: unix_timestamp
select unix_timestamp("2011-12-07 13:01:03");
--指定格式日期转UNIX时间戳函数: unix_timestamp
select unix_timestamp('20111207 13:01:03','yyyyMMdd HH:mm:ss');
--UNIX时间戳转日期函数: from_unixtime
select from_unixtime(1618238391);
select from_unixtime(0, 'yyyy-MM-dd HH:mm:ss');
--日期比较函数: datediff  日期格式要求'yyyy-MM-dd HH:mm:ss' or 'yyyy-MM-dd'
select datediff('2012-12-08','2012-05-09');
--日期增加函数: date_add
select date_add('2012-02-28',10);
--日期减少函数: date_sub
select date_sub('2012-01-1',10);


--抽取日期函数: to_date
select to_date('2009-07-30 04:17:52');
--日期转年函数: year
select year('2009-07-30 04:17:52');
--日期转月函数: month
select month('2009-07-30 04:17:52');
--日期转天函数: day
select day('2009-07-30 04:17:52');
--日期转小时函数: hour
select hour('2009-07-30 04:17:52');
--日期转分钟函数: minute
select minute('2009-07-30 04:17:52');
--日期转秒函数: second
select second('2009-07-30 04:17:52');
--日期转周函数: weekofyear 返回指定日期所示年份第几周
select weekofyear('2009-07-30 04:17:52');
```

```sql
-------Collection Functions 集合函数--------------
--集合元素size函数: size(Map<K.V>) size(Array<T>)
select size(`array`(11,22,33));
select size(`map`("id",10086,"name","zhangsan","age",18));

--取map集合keys函数: map_keys(Map<K.V>)
select map_keys(`map`("id",10086,"name","zhangsan","age",18));

--取map集合values函数: map_values(Map<K.V>)
select map_values(`map`("id",10086,"name","zhangsan","age",18));

--判断数组是否包含指定元素: array_contains(Array<T>, value)
select array_contains(`array`(11,22,33),11);
select array_contains(`array`(11,22,33),66);

--数组排序函数:sort_array(Array<T>)
select sort_array(`array`(12,2,32));
```

```sql
----Mathematical Functions 数学函数-------------
--取整函数: round  返回double类型的整数值部分 （遵循四舍五入）
select round(3.1415926);
--指定精度取整函数: round(double a, int d) 返回指定精度d的double类型
select round(3.1415926,4);
--向下取整函数: floor
select floor(3.1415926);
select floor(-3.1415926);
--向上取整函数: ceil
select ceil(3.1415926);
select ceil(-3.1415926);
--取随机数函数: rand 每次执行都不一样 返回一个0到1范围内的随机数
select rand();
--指定种子取随机数函数: rand(int seed) 得到一个稳定的随机数序列
select rand(3);



--二进制函数:  bin(BIGINT a)
select bin(18);
--进制转换函数: conv(BIGINT num, int from_base, int to_base)
select conv(17,10,16);
--绝对值函数: abs
select abs(-3.9);
```

![截屏2022-03-09 22.30.31](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-09%2022.30.31.jpg)

```sql
-----Conditional Functions 条件函数------------------
--使用之前课程创建好的student表数据
select * from student limit 3;

describe function extended isnull;

--if条件判断: if(boolean testCondition, T valueTrue, T valueFalseOrNull)
select if(1=2,100,200);
select if(sex ='男','M','W') from student limit 3;

--空判断函数: isnull( a )
select isnull("allen");
select isnull(null);

--非空判断函数: isnotnull ( a )
select isnotnull("allen");
select isnotnull(null);

--空值转换函数: nvl(T value, T default_value)
select nvl("allen","itcast");
select nvl(null,"itcast");
--非空查找函数: COALESCE(T v1, T v2, ...)
--返回参数中的第一个非空值；如果所有值都为NULL，那么返回NULL
select COALESCE(null,11,22,33);
select COALESCE(null,null,null,33);
select COALESCE(null,null,null);

--条件转换函数: CASE a WHEN b THEN c [WHEN d THEN e]* [ELSE f] END
select case 100 when 50 then 'tom' when 100 then 'mary' else 'tim' end;
select case sex when '男' then 'male' else 'female' end from student limit 3;

--nullif( a, b ):
-- 如果a = b，则返回NULL，否则返回一个
select nullif(11,11);
select nullif(11,12);

--assert_true(condition)
--如果'condition'不为真，则引发异常，否则返回null
SELECT assert_true(11 >= 0);
SELECT assert_true(-1 >= 0);
```

![截屏2022-03-10 10.54.06](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-10%2010.54.06.jpg)

![截屏2022-03-10 10.55.03](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-10%2010.55.03.jpg)

如果string 转 string 是没有意义的，返回的结果就是null。

```sql
----Data Masking Functions 数据脱敏函数------------
--mask
--将查询回的数据，大写字母转换为X，小写字母转换为x，数字转换为n。
select mask("abc123DEF");
select mask("abc123DEF",'-','.','^'); --自定义替换的字母

--mask_first_n(string str[, int n]
--对前n个进行脱敏替换
select mask_first_n("abc123DEF",4);

--mask_last_n(string str[, int n])
select mask_last_n("abc123DEF",4);

--mask_show_first_n(string str[, int n])
--除了前n个字符，其余进行掩码处理
select mask_show_first_n("abc123DEF",4);

--mask_show_last_n(string str[, int n])
select mask_show_last_n("abc123DEF",4);

--mask_hash(string|char|varchar str)
--返回字符串的hash编码。
select mask_hash("abc123DEF");

```

```sql
----- Misc. Functions 其他杂项函数---------------

--如果你要调用的java方法所在的jar包不是hive自带的 可以使用add jar添加进来
--hive调用java方法: java_method(class, method[, arg1[, arg2..]])
select java_method("java.lang.Math","max",11,22);

--反射函数: reflect(class, method[, arg1[, arg2..]])
select reflect("java.lang.Math","max",11,22);

-- hive调用python方法：
add file  local的位置/py_file.py; 

select transform(col1,col2,col3...) using 'python py_file.py' as (out1,out2,out3...) from table;
# transform 的参数col1，col2，col3... 作为python脚本的输入，而out1,out2,out3...作为输出字段。利用这个可以把hive自身所带的函数不能或不方便处理的任务，转用python写。毕竟人生苦短我还是喜欢python。
-------------------------------------------------------------------------------
-- hive用python例子
# use.py文件内容。
import sys
import datetime
 
for line in sys.stdin:                                 //接收输入
  line = line.strip()    															 //去除头尾的空格
  userid, movieid, rating, unixtime = line.split('\t') //分割一条数据
  weekday = datetime.datetime.fromtimestamp(float(unixtime)).isoweekday()//转化unixtime时间戳为日期时间，获取对应的星期值
  print '\t'.join([userid, movieid, rating, str(weekday)])//输出清洗后的数据

# hive内
add file /user/hive/warehouse/use.py;  # 注意 python脚本的路径是没有“” 的！而且路径是local的就行了，不是hdfs的路径。

INSERT OVERWRITE TABLE u_data_new
SELECT
  TRANSFORM (userid, movieid, rating, unixtime)    	//输入值（基表）
  USING 'python weekday_mapper.py'                  //使用脚本清洗 # 注意写个python 放前面，不然不执行。 
  AS (userid, movieid, rating, weekday)             //输出值（子表）
FROM u_data;   																			//基表
```

## 我在centos搭建的Hadoop集群和hive工具，实验如下：

### 1、创建原始表

```sql
drop table t_python_test;
create table t_python_test(
    id int,
    sex string,
    age int
)row format delimited fields terminated by "\t";

load data local inpath '/opt/data/python_test.txt' into table t_python_test;
# 加载数据
select * from t_python_test;  # 原表如下
```

![截屏2022-03-10 23.38.15](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-10%2023.38.15.jpg)

### 2、写python脚本；

```python
# coding:utf-8
import sys
try:
    for line in sys.stdin: 
# python脚本以sys.stdin 读hive输入。其输入是以字段拼接字段而形成的字符串，这样的字符串有多行。
        line = line.strip() # 读取一行，去头尾的空格。
        [t_id,t_sex,t_age] = line.split('\t') 
        #print( t_id,t_sex,t_age)
        if t_sex == 'm':
            t_age =int(t_age) + 1
            t_sex = 'super_m'
        print('\t'.join([str(t_id),t_sex,str(t_age)])) 
# python脚本输出一个字符串，hive读取python脚本输出的时候会根据 字段名 自动切分好此字符串。  
except
    print('\t'.join(['0','modify failed','0']))
```

​		注意：一定要记得在hive、Hadoop环境之外先用python检查一下有没有什么python编写错误，因为在hive里运行的速度太慢了，每一次排错都要花太多太多的时间，并且报错以后的提示看不到错在哪里，只是简单的说你的自定义脚本有问题Execution Error, return code 20003 from org.apache.hadoop.hive.ql.exec.mr.MapRedTask. An error occurred when trying to close the Operator running your custom script.。

```bash
cat python_test.txt | python hive_python_test.py  # 在hive以外运行一遍，用来排错。
```

### 3、hive调用python脚本

```sql
add file /opt/data/hive_python_test.py;  # 添加python文件到hdfs，路径就是本地，没引号
drop table t_python_test_new;

create table  t_python_test_new as select
transform(id,sex,age )                   # 从原表中 选这些字段组成str汇成多行，输入脚本
using 'python hive_python_test.py'       # python 运行 python脚本
as (id,sex,age)												   # 将脚本的输出，按照这里定义的字段切分好。
from t_python_test;

select * from t_python_test_new;         # 如下
```

![截屏2022-03-11 00.08.30](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-11%2000.08.30.jpg)



## 网上教学案例

![截屏2022-03-10 11.08.17](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-10%2011.08.17.jpg)

![截屏2022-03-10 11.09.04](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-10%2011.09.04.jpg)

![截屏2022-03-10 11.11.35](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-10%2011.11.35.jpg)





![截屏2022-03-10 11.15.44](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-10%2011.15.44.jpg)

![截屏2022-03-10 11.21.57](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-10%2011.21.57.jpg)

我这里还是选择用python实现，毕竟越傻瓜越方便嘛。





## 函数高阶介绍

![截屏2022-03-12 18.00.08](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-12%2018.00.08.jpg)

![截屏2022-03-12 18.03.01](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-12%2018.03.01.jpg)

![截屏2022-03-12 18.08.30](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-12%2018.08.30.jpg)

![截屏2022-03-14 15.23.46](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.23.46.jpg)

![截屏2022-03-14 15.11.11](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.11.11.jpg)

![截屏2022-03-14 15.22.53](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.22.53.jpg)

![截屏2022-03-14 15.24.24](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.24.24.jpg)

![截屏2022-03-14 15.26.18](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.26.18.jpg)

![截屏2022-03-14 15.27.11](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.27.11.jpg)



![截屏2022-03-14 15.32.25](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.32.25.jpg)

![截屏2022-03-14 15.33.37](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.33.37.jpg)

![截屏2022-03-14 15.43.28](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.43.28.jpg)

注意：以上的col1 col2 col3 是炸开的udtf表的 字段的名字。

![截屏2022-03-14 15.50.03](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.50.03.jpg)

![截屏2022-03-14 15.50.20](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.50.20.jpg)

![截屏2022-03-14 15.51.29](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.51.29.jpg)

![截屏2022-03-14 15.53.42](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.53.42.jpg)

![截屏2022-03-14 15.54.56](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.54.56.jpg)

![截屏2022-03-14 15.56.13](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.56.13.jpg)

![截屏2022-03-14 15.58.03](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.58.03.jpg)

![截屏2022-03-14 15.59.55](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2015.59.55.jpg)

coalsesce函数 将null值 换成0.

![截屏2022-03-14 16.01.51](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2016.01.51.jpg)

struct类型：

STRUCT：STRUCT可以包含不同数据类型的元素。这些元素可以通过”点语法”的方式来得到所需要的元素，比如user是一个STRUCT类型，那么可以通过user.address得到这个用户的地址。

```sql
 create table student_test(id int,info struct<name:string,age:int>)
 row format delimited fields terminated by "," 
 collection items terminated by ":";
```

文件

 [root@hello110 data]# vi student_test
 1001,zhangsan:24
 1002,lisi:25
 1003,xiaoming:26
 1004,dongdong:27

```sql
select   info.name,info.age from student_test;
OK
name    age
zhangsan        24
lisi    25
xiaoming        26
dongdong        27
```

![截屏2022-03-14 16.16.58](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2016.16.58.jpg)

Struct结构用于按字典顺序排序，从左往右地按字段比较。![截屏2022-03-14 16.25.41](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2016.25.41.jpg)

![截屏2022-03-14 16.42.12](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2016.42.12.jpg)

![截屏2022-03-14 16.43.08](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2016.43.08.jpg)

![截屏2022-03-14 16.46.47](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2016.46.47.jpg)

![截屏2022-03-14 16.56.32](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2016.56.32.jpg)

![截屏2022-03-14 16.57.33](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2016.57.33.jpg)

![截屏2022-03-14 19.23.56](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2019.23.56.jpg)

注意：左侧的含义，a c 就不属于左侧的定义。

![截屏2022-03-14 19.26.16](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2019.26.16.jpg)

![截屏2022-03-14 19.30.01](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2019.30.01.jpg)

用窗口函数聚合 和 普通聚合函数聚合的区别：

​		方便看清分组的细节，而且即便不用聚合函数，可以select 和分组无关的字段。（这在一般的分组情况下是不可以的）。

![截屏2022-03-14 20.03.02](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2020.03.02.jpg)

注意：窗口函数 over 内的order by 不是全局排序，而是分组内排序了。和普通的order by 作用域不一样。

![截屏2022-03-14 20.09.14](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2020.09.14.jpg)

![截屏2022-03-14 20.59.09](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2020.59.09.jpg)

![截屏2022-03-14 21.00.51](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2021.00.51.jpg)

注意：一旦窗口函数over内有了 order by 排序 ，sum（） 会变成累计求和。

![截屏2022-03-14 21.05.03](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2021.05.03.jpg)

![截屏2022-03-14 21.11.07](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2021.11.07.jpg)

这个窗口表达式的向前几行到当前行 ，或者向后几行，要注意，求和就变成了这个窗口表达式的范围内的求和了。



![截屏2022-03-14 21.15.59](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2021.15.59.jpg)

![截屏2022-03-14 21.17.05](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2021.17.05.jpg)

 ![截屏2022-03-14 21.19.24](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2021.19.24.jpg)

![截屏2022-03-14 21.21.36](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2021.21.36.jpg)

以上三个窗口排序函数 适合 top N 业务分析。

![截屏2022-03-14 21.26.35](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2021.26.35.jpg)

```sql
# 需求：找出每个用户访问pv最多的top 3，重复并列不考虑。
select * from (
select cookieid,createtime,pv,
  row_number() over(partition by cookieid order by pv desc) as seq from website_pv_info
)tmp where tmp.seq<4;
```

![截屏2022-03-14 22.06.32](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2022.06.32.jpg)

7个要平均分成3份，只能是3、2、2这样分。

![截屏2022-03-14 22.13.37](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2022.13.37.jpg)

![截屏2022-03-14 22.15.22](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2022.15.22.jpg)

![截屏2022-03-14 22.21.23](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2022.21.23.jpg)

![截屏2022-03-14 22.24.03](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2022.24.03.jpg)





![截屏2022-03-14 22.25.49](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2022.25.49.jpg)

![截屏2022-03-14 22.35.18](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2022.35.18.jpg)

![截屏2022-03-14 22.37.18](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2022.37.18.jpg)

![截屏2022-03-14 22.38.46](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2022.38.46.jpg)

![截屏2022-03-14 22.39.26](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2022.39.26.jpg)

![截屏2022-03-14 22.48.28](Hive%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE%E4%B8%8E%E5%87%BD%E6%95%B0%E3%80%81%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BD%BF%E7%94%A8.assets/%E6%88%AA%E5%B1%8F2022-03-14%2022.48.28.jpg)

