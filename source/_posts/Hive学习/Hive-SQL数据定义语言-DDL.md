---
layout: posts
title: Hive_SQL数据定义语言_DDL
date: 2022-10-29 15:11:19
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "hive"
tags:
- "hive"
- "sql"
---

简介 <!--more-->
# 简介

![截屏2022-02-24 15.38.54](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2015.38.54-7028285-7028320.jpg)

![截屏2022-02-24 16.23.09](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2016.23.09-7028320.jpg)

![截屏2022-02-24 16.27.45](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2016.27.45-7028320.jpg)

![截屏2022-02-24 16.35.28](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2016.35.28-7028320.jpg)

![截屏2022-02-24 16.38.17](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2016.38.17-7028320.jpg)

![截屏2022-02-24 16.39.02](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2016.39.02-7028320.jpg)

![截屏2022-02-24 16.39.45](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2016.39.45-7028320.jpg)



![截屏2022-02-24 16.40.13](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2016.40.13-7028320.jpg)

![截屏2022-02-24 16.43.38](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2016.43.38-7028320.jpg)

![截屏2022-02-24 16.46.46](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2016.46.46-7028320.jpg)

![截屏2022-02-24 16.48.48](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2016.48.48-7028320.jpg)

![截屏2022-02-24 16.56.03](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2016.56.03-7028320.jpg)

![截屏2022-02-24 17.05.34](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2017.05.34-7028320.jpg)

![截屏2022-02-24 17.06.03](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2017.06.03-7028320.jpg)

![截屏2022-02-24 17.08.04](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2017.08.04-7028320.jpg)

![截屏2022-02-24 17.10.07](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2017.10.07-7028320.jpg)

![截屏2022-02-24 17.11.08](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2017.11.08-7028320.jpg)

![截屏2022-02-24 21.23.10](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2021.23.10-7028320.jpg)

内嵌模式安装（这只是简介，具体的安装配置教程参考之前的hive安装笔记。）：

![截屏2022-02-24 21.28.06](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2021.28.06-7028320.jpg)

本地模式安装（这只是简介，具体的安装配置教程参考之前的hive安装笔记。）：

![截屏2022-02-24 21.33.58](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2021.33.58-7028320.jpg)

![截屏2022-02-24 21.39.15](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2021.39.15-7028320.jpg)

远程模式安装：

![截屏2022-02-24 21.44.17](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2021.44.17-7028320.jpg)

![截屏2022-02-24 21.48.19](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2021.48.19-7028320.jpg)

方式3 远程方式安装，这才是hive主要应用场景（这只是简介，具体的安装配置教程参考之前的hive安装笔记。）：

![截屏2022-02-24 21.50.23](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2021.50.23-7028320.jpg)

![截屏2022-02-24 21.54.14](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2021.54.14-7028320.jpg)

![截屏2022-02-24 22.00.36](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2022.00.36-7028320.jpg)

![截屏2022-02-24 22.01.58](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2022.01.58-7028320.jpg)

![截屏2022-02-24 22.12.10](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2022.12.10-7028320.jpg)



​		我们在用hive强行新建一个表，并插入数据时，速度很慢实际是运行一个java程序，此时我们打开yarn的监测端口可以看见一个mapreduce程序正在运行。

![截屏2022-03-01 12.03.53](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-01%2012.03.53-7028320.jpg)

![截屏2022-03-01 12.04.17](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-01%2012.04.17-7028320.jpg)

![截屏2022-03-01 12.04.39](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-01%2012.04.39-7028320.jpg)

​		卧槽插入一条数据花了182秒，可见hive真的不是sql。

​		映射成表才是正解

```bash
vim user.txt
1,zhangsan
2,lisi

bin/hadoop fs -put user.txt / # 用Hadoop命令把user.txt 传到hdfs的根目录下。

此时用9870端口查看yarn的根目录，可见多了一个user.txt文件。
```

![截屏2022-03-01 12.38.49](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-01%2012.38.49-7028320.jpg)

​		默认情况下，hive在 /user/hive/warehouse 中创建库和表。将我们刚才放在hdfs根目录的user.txt移动到 /user/hive/warehouse 与之对应的表中。

```bash
bin/hadoop  fs  -mv  /user.txt  /user/hive/warehouse/test.db/table_test
```

​		现在可以看见有东西存进来了，但是新的内容都是显示null，

![截屏2022-03-01 14.03.56](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-01%2014.03.56-7028320.jpg)

​		hive建立表的时间加上分隔符

```sql
create table t_user(id int,name varchar(20))row format delimited fields terminated by ',';
```

​		把user.txt 文件映射到指定位置的表中

```bash
bin/hadoop fs -put user.txt /user/hive/warehouse/test.db/t_user
```

​		再在hive中 select * from t_user; 可见

![截屏2022-03-01 14.14.14](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-01%2014.14.14-7028320.jpg)

​		成功映射好了。

​		注意文件映射为表，hive会自己尝试着把类型对应，比如文件类型是int，映射的表中类型是varchar，那么hive可以帮我们自动类型转换，但是如果文件中类型是str字符串类型，表中定义时就定义了是int类型，hive自动类型转换str转int是转不过来的，有点像java的自动类型转换的级别特点。

​		所以建立表时，要把字段顺序和字段类型和文件保持一致。



![截屏2022-02-24 22.26.28](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-02-24%2022.26.28-7028320.jpg)

# Hive SQL数据定义语言 DDL

​		![截屏2022-03-01 21.11.51](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-01%2021.11.51-7028320.jpg)

![截屏2022-03-02 17.10.00](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-02%2017.10.00-7028320.jpg)

![截屏2022-03-02 17.11.09](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-02%2017.11.09-7028320.jpg)

![截屏2022-03-02 17.18.40](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-02%2017.18.40-7028320.jpg)



<img src="Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-02%2017.19.35-7028320.jpg" alt="截屏2022-03-02 17.19.35" style="zoom:50%;" />

<img src="Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-02%2017.32.35-7028320.jpg" alt="截屏2022-03-02 17.32.35" style="zoom:50%;" />

![截屏2022-03-02 17.35.34](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-02%2017.35.34-7028320.jpg)

![截屏2022-03-02 17.40.16](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-02%2017.40.16-7028320.jpg)



![截屏2022-03-02 17.44.33](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-02%2017.44.33-7028320.jpg)

![截屏2022-03-02 17.47.18](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-02%2017.47.18-7028320.jpg)

![截屏2022-03-02 17.48.21](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-02%2017.48.21-7028320.jpg)

![截屏2022-03-02 18.30.46](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-02%2018.30.46-7028320.jpg)

![截屏2022-03-02 18.31.03](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-02%2018.31.03-7028320.jpg)

![截屏2022-03-03 11.39.33](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-03%2011.39.33-7028320.jpg)

​	新建完sql 记得新增数据源 configure data source，就是apache hive。改一下主机名，用户名，测试一下，然后链接。

![截屏2022-03-03 11.46.51](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-03%2011.46.51-7028320.jpg)

![截屏2022-03-03 17.00.07](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-03%2017.00.07-7028320.jpg)

![截屏2022-03-03 22.37.00](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-03%2022.37.00-7028320.jpg)

​		把文件放到hive表对应的的位置，马上就映射成功了。

![截屏2022-03-03 22.40.45](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-03%2022.40.45-7028320.jpg)



![截屏2022-03-03 22.48.14](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-03%2022.48.14-7028320.jpg)



![截屏2022-03-03 23.05.05](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-03%2023.05.05-7028320.jpg)



![](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-03%2023.08.06-7028320.jpg)

所以优先考虑 \001 作为分隔符。

![截屏2022-03-03 23.14.15](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-03%2023.14.15-7028320.jpg)

注意上面的 / 是hdfs的根目录，不是系统根目录。![截屏2022-03-03 23.17.57](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-03%2023.17.57-7028320.jpg)

```bash
hadoop fs -chmod -R 777 / # 可以让 node01:9870 浏览器中修改hdfs的目录文件夹。给了权限。

# 更加传统的方法是
# 查看文件
hadoop fs -ls /user/hive/warehouse
# 删除文件、目录
hadoop fs -rm  /data

# 有了新的hdfs的文件夹以后，可以hive建表location到 指定的hdfs下的位置了。将文件Hadoop fs -put 文件 新位置 ; 传过来即可映射。
```

![截屏2022-03-04 00.10.57](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-04%2000.10.57-7028320.jpg)

![截屏2022-03-04 00.12.03](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-04%2000.12.03-7028320.jpg)

![截屏2022-03-04 10.46.05](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-04%2010.46.05-7028320.jpg)

![截屏2022-03-04 10.47.46](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-04%2010.47.46-7028320.jpg)

![截屏2022-03-04 10.48.29](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-04%2010.48.29-7028320.jpg)

![截屏2022-03-04 10.50.34](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-04%2010.50.34-7028320.jpg)

![截屏2022-03-04 10.56.21](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-04%2010.56.21-7028320.jpg)

​		external创建外部表，但是不添加location，存放位置还是在/usr/hive/warehouse 中。但是它还是外部表，删除操作保留原文件。

内部表还是外部表由external存在与否决定，location在哪里和这点没关系。

![截屏2022-03-05 11.05.12](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.05.12-7028320.jpg)

![截屏2022-03-05 11.10.19](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.10.19-7028320.jpg)

![截屏2022-03-05 11.12.14](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.12.14-7028320.jpg)

![截屏2022-03-05 11.13.21](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.13.21-7028320.jpg)

![截屏2022-03-05 11.18.49](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.18.49-7028320.jpg)

![截屏2022-03-05 11.20.01](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.20.01-7028320.jpg)

![截屏2022-03-05 11.21.51](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.21.51-7028320.jpg)

​		分区表数据不能想原来一样 put 进指定位置映射就完事，需要每一个源文件在 hive console控制台内 静态加载到 hdfs 对应表中。

![截屏2022-03-05 11.26.44](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.26.44-7028320.jpg)

![截屏2022-03-05 11.31.46](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.31.46-7028320.jpg)

![截屏2022-03-05 11.34.17](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.34.17-7028320.jpg)

![截屏2022-03-05 11.36.44](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.36.44-7028320.jpg)

​		我的分区优化后的查询用时1分59秒，没有分区的查询时间是2分7秒。



![截屏2022-03-05 11.39.07](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.39.07-7028320.jpg)

![截屏2022-03-05 11.40.39](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.40.39-7028320.jpg)

![截屏2022-03-05 11.42.42](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.42.42-7028320.jpg)

![截屏2022-03-05 11.45.56](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.45.56-7028320.jpg)

![截屏2022-03-05 11.53.13](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.53.13-7028320.jpg)

![截屏2022-03-05 11.56.25](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2011.56.25-7028320.jpg)

​		第三点中，底层原数据没有变化，显示的分区字段其实是 hive 中生成的。

```sql
set hive.exec.dynamic.partition=true;
set hive.exec.dynamic.partition.mode=nonstrict;

create table t_all_hero_part_dynamic(
                                id int,
                                name string,
                                hp_max int,
                                mp_max int,
                                attack_max int,
                                defense_man int,
                                attack_range string,
                                role_main string,
                                role_assist string
)partitioned by (role string) row format delimited fields terminated by "\t";
create table t_all_hero(
                                        id int,
                                        name string,
                                        hp_max int,
                                        mp_max int,
                                        attack_max int,
                                        defense_man int,
                                        attack_range string,
                                        role_main string,
                                        role_assist string
)row format delimited fields terminated by "\t";

# 动态插入分区表\动态分区。
insert into table t_all_hero_part_dynamic partition(role)
select tmp.*,tmp.role_main from t_all_hero tmp;

```

![截屏2022-03-05 20.04.18](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2020.04.18-7028320.jpg)

![截屏2022-03-05 20.06.10](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2020.06.10-7028320.jpg)

​		这里可以回顾一下，之前学习的算法与数据结构中的 散列表。![截屏2022-03-05 20.14.10](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2020.14.10-7028320.jpg)

![截屏2022-03-05 20.18.44](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2020.18.44-7028320.jpg)

![截屏2022-03-05 20.19.55](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2020.19.55-7028320.jpg)

![截屏2022-03-05 20.29.27](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2020.29.27-7028320.jpg)

![截屏2022-03-05 20.34.15](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2020.34.15-7028320.jpg)

​		分桶表还可以提高抽样的效率。

![截屏2022-03-05 21.20.06](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2021.20.06-7028320.jpg)

![截屏2022-03-05 21.24.12](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2021.24.12-7028320.jpg)

![截屏2022-03-05 21.26.06](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2021.26.06-7028320.jpg)

![截屏2022-03-05 21.28.36](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2021.28.36-7028320.jpg)

![截屏2022-03-05 21.31.36](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2021.31.36-7028320.jpg)

![截屏2022-03-05 21.34.40](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2021.34.40-7028320.jpg)

insert into trans_student values(1,'chenyushao',29);

![截屏2022-03-05 21.37.31](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2021.37.31-7028320.jpg)

![截屏2022-03-05 21.39.32](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2021.39.32-7028320.jpg)

​		可以看见其实hive没有删除原文件，只是用了新的 “事务操作” 产生新的版本而已。

```bash
set hive.support.concurrency=true;
set hive.enforce.bucketing=true;
set hive.exec.dynamic.partition.mode=nonstrict;
set hive.txn.manager=org.apache.hadoop.hive.ql.lockmgr.DbTxnManager;
set hive.compactor.initiator.on=true;
set hive.compactor.worker.threads=1;

drop table trans_student;
create table trans_student(
    id int,
    name string,
    age int
)clustered by (id) into 2 buckets stored as orc TBLPROPERTIES('transactional'='true');

insert into trans_student values(1,'chenyushao',29);  # 插入数据到事务表， 速度实在是太慢了！3分钟。
update trans_student set age=20 where id=1;
delete from trans_student where id =1;
select * from trans_student;
```



![截屏2022-03-05 21.40.45](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2021.40.45-7028320.jpg)

​		基于select 创建视图，只看前面5项。

![截屏2022-03-05 21.49.13](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2021.49.13-7028320.jpg)

​		

![截屏2022-03-05 21.51.26](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2021.51.26-7028320.jpg)

![截屏2022-03-05 21.53.39](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2021.53.39-7028320.jpg)

![截屏2022-03-05 21.55.03](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2021.55.03-7028320.jpg)

![截屏2022-03-05 21.58.49](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2021.58.49-7028320.jpg)

![截屏2022-03-05 22.04.12](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2022.04.12-7028320.jpg)

​		注意：view只是让我们方便查看语句，并不是真的优化了查询的性能。



![截屏2022-03-05 22.07.09](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2022.07.09-7028320.jpg)

![截屏2022-03-05 22.11.05](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2022.11.05-7028320.jpg)

![截屏2022-03-05 22.15.09](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2022.15.09-7028320.jpg)

![截屏2022-03-05 22.21.13](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2022.21.13-7028320.jpg)

![截屏2022-03-05 22.24.30](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2022.24.30-7028320.jpg)

![截屏2022-03-05 22.27.46](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2022.27.46-7028320.jpg)

![截屏2022-03-05 22.31.42](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2022.31.42-7028320.jpg)

![截屏2022-03-05 22.32.16](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2022.32.16-7028320.jpg)

![截屏2022-03-05 22.33.46](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2022.33.46-7028320.jpg)

![截屏2022-03-05 22.34.37](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-05%2022.34.37-7028320.jpg)

```bash
create table student(
    id int ,
    name string,
    sex string,
    age string,
    studept string
)row format delimited fields terminated by",";

drop table student_trans;
create table student_trans(
    sno int,
    sname string,
    sdept string
)clustered by (sno) into 2 buckets stored as orc TBLPROPERTIES ('transactional'='true'); # 新建一个分桶事务表。
insert into table student_trans select id,name,studept from student; # 从普通表插入到分桶事务表中。

create materialized view student_trans_agg
    as select sdept ,count(*)as dept_cnt from student_trans group by sdept; # 创建物化视图（因为这个查询经常用，才会作一个物化视图。）

select sdept,count(*) from student_trans group by sdept;  # 速度快，直接从物化视图的存档继续。
explain select sdept,count(*) from student_trans group by sdept; # 可见从物化视图继续的，并不是从student_trans。
alter materialized view student_trans_agg disable rewrite; # 关闭此物化视图的重写属性。
select sdept,count(*) from student_trans group by sdept;  # 速度瞬间变慢。
alter materialized view student_trans_agg enable rewrite;
select sdept,count(*) from student_trans group by sdept;

```

![截屏2022-03-06 21.19.07](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.19.07-7028320.jpg)

![截屏2022-03-06 21.20.45](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.20.45-7028320.jpg)

![截屏2022-03-06 21.21.14](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.21.14-7028320.jpg)

![截屏2022-03-06 21.26.11](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.26.11-7028320.jpg)

![截屏2022-03-06 21.27.18](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.27.18-7028320.jpg)

![截屏2022-03-06 21.28.22](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.28.22-7028320.jpg)

![截屏2022-03-06 21.30.57](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.30.57-7028320.jpg)

![截屏2022-03-06 21.34.19](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.34.19-7028320.jpg)

![截屏2022-03-06 21.37.05](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.37.05-7028320.jpg)

![截屏2022-03-06 21.38.29](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.38.29-7028320.jpg)

![截屏2022-03-06 21.42.50](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.42.50-7028320.jpg)

![截屏2022-03-06 21.45.25](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.45.25-7028320.jpg)

![截屏2022-03-06 21.50.02](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.50.02-7028320.jpg)

![截屏2022-03-06 21.52.17](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.52.17-7028320.jpg)

![截屏2022-03-06 21.53.09](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.53.09-7028320.jpg)

![截屏2022-03-06 21.55.20](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.55.20-7028320.jpg)

![截屏2022-03-06 21.59.01](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2021.59.01-7028320.jpg)

​		1、在hive的console创建一个分区的表；

​		2、直接用hdfs命令创建分区文件夹；hadoop fs -mkdir -p /user/hive/marehouse/databasename/tablename/role=sheshou（分区名）

​		3、hdfs 命令直接 put 数据上 对应的分区位置；

​		4、hive 的select 看不见上面的分区表；

​		5、用msck 修复分区，msck repair table 【tablename】 add partition；add partition 可以不写、msck默认添加分区。

​		6、hive可以操作新分区表了。



​		1、hdfs 直接删除分区表的一个分区；

​		2、hive 看不见变化，还误以为没有变；

​		3、msck repair table 【tablename】drop partition； 修复删除分区。

​		4、hive 正常了。

【sync】同步hive 的分区表元存储 与 hdfs上的内容。操作同上。

![截屏2022-03-06 22.18.57](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2022.18.57-7028320.jpg)

![截屏2022-03-06 22.20.03](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2022.20.03-7028320.jpg)

![截屏2022-03-06 22.21.16](Hive%20SQL%E6%95%B0%E6%8D%AE%E5%AE%9A%E4%B9%89%E8%AF%AD%E8%A8%80%20DDL.assets/%E6%88%AA%E5%B1%8F2022-03-06%2022.21.16-7028320.jpg)



