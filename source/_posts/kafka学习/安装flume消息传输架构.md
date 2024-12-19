---
layout: posts
title: 安装flume消息传输架构
date: 2022-10-29 15:39:56
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "kafka"
tags:
- "flume"
---

简介 <!--more-->
# flume 是个啥？

![截屏2022-05-05 11.04.39](%E5%AE%89%E8%A3%85flume%E6%B6%88%E6%81%AF%E4%BC%A0%E8%BE%93%E6%9E%B6%E6%9E%84.assets/%E6%88%AA%E5%B1%8F2022-05-05%2011.04.39-7028830.jpg)

![截屏2022-05-05 11.04.57](%E5%AE%89%E8%A3%85flume%E6%B6%88%E6%81%AF%E4%BC%A0%E8%BE%93%E6%9E%B6%E6%9E%84.assets/%E6%88%AA%E5%B1%8F2022-05-05%2011.04.57.jpg)

​		消息传输框架，输出不一定是hdfs 也可以是kafka，输入也可以是kafka，flume常用于 对日志文件的读取，最早开发就是用来对接Hadoop的hdfs的。

# 安装配置

我在安装配置完 Hadoop的基础上，进行flume的安装配置。先从node01开始。

下载地址

https://downloads.apache.org/flume/

解压

` tar -zvxf apache-flume-1.9.0-bin.tar.gz -C /opt`

改个名方便识别

` mv apache-flume-1.9.0-bin flume `

node01配置环境变量

```shell 
vim /etc/profile

export FLUME_HOME=/opt/flume
export PATH=:$FLUME_HONE/bin

source /etc/profile   # 生效
```

修改配置文件

```shell 
 cd flume/conf
 cp flume-env.sh.template flume-env.sh
 vim flume-env.sh
 # 里面添加java环境变量
```

![截屏2022-05-05 10.57.19](%E5%AE%89%E8%A3%85flume%E6%B6%88%E6%81%AF%E4%BC%A0%E8%BE%93%E6%9E%B6%E6%9E%84.assets/%E6%88%AA%E5%B1%8F2022-05-05%2010.57.19.jpg)

```shell
vim slave.conf

# 主要作用是监听目录中的新增数据，采集到数据之后，输出到avro （输出到agent）
# 注意：Flume agent的运行，主要就是配置source channel sink
# 下面的a1就是agent的代号，source叫r1 channel叫c1 sink叫k1
 
a1.sources = r1
a1.sinks = k1
a1.channels = c1
 
#具体定义source  
a1.sources.r1.type = spooldir
#先创建此目录，保证里面空的  
a1.sources.r1.spoolDir = /opt/flume/logs
 
#对于sink的配置描述 使用avro日志做数据的消费，以后用kafka，也要改动这里
a1.sinks.k1.type = avro
# hostname是最终传给的主机名称或者ip地址
a1.sinks.k1.hostname = node01
a1.sinks.k1.port = 44444
 
#对于channel的配置描述 使用文件做数据的临时缓存 这种的安全性要高
a1.channels.c1.type = file
a1.channels.c1.checkpointDir = /opt/flume/checkpoint # 先创建
a1.channels.c1.dataDirs = /opt/flume/data            # 先创建
 
#通过channel c1将source r1和sink k1关联起来
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

分发 flume 文件夹到node02、node03两台从属机器。

```shell
scp -r /opt/flume node03:/opt/
scp -r /opt/flume node02:/opt/
```

node02、node03配置环境变量

```shell
vim /etc/profile

export FLUME_HOME=/opt/flume
export PATH=:$FLUME_HONE/bin

source /etc/profile   # 生效
```

在node01的flume的conf文件夹下创建一个master.conf文件：

```shell
vim master.conf 

# 获取slave1,2上的数据，聚合起来，传到hdfs上面
# 注意：Flume agent的运行，主要就是配置source channel sink
# 下面的a1就是agent的代号，source叫r1 channel叫c1 sink叫k1
 
a1.sources = r1
a1.sinks = k1
a1.channels = c1
 
#对于source的配置描述 监听avro
a1.sources.r1.type = avro
# hostname是最终传给的主机名称或者ip地址
a1.sources.r1.bind = node01
a1.sources.r1.port = 44444
 
 
#定义拦截器，为消息添加时间戳  
a1.sources.r1.interceptors = i1  
a1.sources.r1.interceptors.i1.type = org.apache.flume.interceptor.TimestampInterceptor$Builder
 
#对于sink的配置描述 传递到hdfs上面
a1.sinks.k1.type = hdfs  
#集群的nameservers名字
#单节点的直接写：hdfs://主机名(ip):9000/xxx
 
a1.sinks.k1.hdfs.path = hdfs://node01:9000/flume/%Y%m%d  
a1.sinks.k1.hdfs.filePrefix = events-  
a1.sinks.k1.hdfs.fileType = DataStream  
#不按照条数生成文件  
a1.sinks.k1.hdfs.rollCount = 0  
#HDFS上的文件达到128M时生成一个文件  
a1.sinks.k1.hdfs.rollSize = 134217728  
#HDFS上的文件达到60秒生成一个文件  
a1.sinks.k1.hdfs.rollInterval = 60  
 
 
#对于channel的配置描述 使用内存缓冲区域做数据的临时缓存
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100
 
#通过channel c1将source r1和sink k1关联起来
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1 
```



## 检查是否安装配置成功

启动Hadoop、启动zookeeper。

```shell
hadoop/bin start-all.sh

# zk 每个机器都要手动启动
zookeeper/bin zkServer.sh start
zookeeper/bin zkServer.sh start
zookeeper/bin zkServer.sh start
```

启动flume集群

```shell 
cd /opt/flume
# master端
bin/flume-ng agent -n a1 -c conf -f conf/master.conf -Dflume.root.logger=INFO,console

# slave端
bin/flume-ng agent -n a1 -c conf -f conf/slave.conf -Dflume.root.logger=INFO,console
```

在node02 被监听的/opt/flume/logs文件夹中 vim 一个文件

```shell
vim test1

chenyushao is a good man!

vim test2

okokokokok
```

从属终端node02可见

![截屏2022-05-05 18.44.24](%E5%AE%89%E8%A3%85flume%E6%B6%88%E6%81%AF%E4%BC%A0%E8%BE%93%E6%9E%B6%E6%9E%84.assets/%E6%88%AA%E5%B1%8F2022-05-05%2018.44.24.jpg)

主机终端node01可见

![截屏2022-05-05 18.45.16](%E5%AE%89%E8%A3%85flume%E6%B6%88%E6%81%AF%E4%BC%A0%E8%BE%93%E6%9E%B6%E6%9E%84.assets/%E6%88%AA%E5%B1%8F2022-05-05%2018.45.16.jpg)

在Hadoop配置的hdfs监听端口可见

![截屏2022-05-05 18.46.32](%E5%AE%89%E8%A3%85flume%E6%B6%88%E6%81%AF%E4%BC%A0%E8%BE%93%E6%9E%B6%E6%9E%84.assets/%E6%88%AA%E5%B1%8F2022-05-05%2018.46.32.jpg)







## 补充：guava版本不一致报错

如果运行之后报错如下：

![截屏2022-05-05 17.38.13](%E5%AE%89%E8%A3%85flume%E6%B6%88%E6%81%AF%E4%BC%A0%E8%BE%93%E6%9E%B6%E6%9E%84.assets/%E6%88%AA%E5%B1%8F2022-05-05%2017.38.13.jpg)

​		报错原因：我的Hadoop的guava 版本和flume 中的guava 版本不一样。

![截屏2022-05-05 17.44.34](%E5%AE%89%E8%A3%85flume%E6%B6%88%E6%81%AF%E4%BC%A0%E8%BE%93%E6%9E%B6%E6%9E%84.assets/%E6%88%AA%E5%B1%8F2022-05-05%2017.44.34.jpg)

​		解决办法：删除低版本的 guava ，把高版本的 guava 复制一份放在低版本处。

```shell
rm /opt/flume/lib/guava-11.0.2.jar

cp /opt/hadoop/hadoop/share/hadoop/common/lib/guava-27.0-jre.jar /opt/flume/lib/

# 把guava文件分发到其他几台机器中。
scp -r /opt/hadoop/hadoop/share/hadoop/common/lib/guava-27.0-jre.jar node03:/opt/flume/lib/
scp -r /opt/hadoop/hadoop/share/hadoop/common/lib/guava-27.0-jre.jar node02:/opt/flume/lib/

# 每台机器上都删除老版本guava
rm -rf /opt/flume/lib/guava-11.0.2.jar 
```

