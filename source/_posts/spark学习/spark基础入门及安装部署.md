---
layout: posts
title: spark基础入门及安装部署
date: 2022-10-29 22:41:18
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "spark"
---

简介 <!--more-->

![截屏2022-03-27 11.18.35](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-27%2011.18.35-7050545.jpg)

![截屏2022-03-27 11.20.09](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-27%2011.20.09.jpg)

![截屏2022-03-27 11.26.39](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-27%2011.26.39.jpg)

​		说人话就是 ，spark 仅仅替代了 mapreduce 计算框架。

![截屏2022-03-28 10.34.15](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-28%2010.34.15.jpg)

![截屏2022-03-28 10.53.00](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-28%2010.53.00.jpg)

![截屏2022-03-28 10.54.58](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-28%2010.54.58.jpg)

![截屏2022-03-28 17.26.13](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-28%2017.26.13.jpg)

![截屏2022-03-28 17.30.02](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-28%2017.30.02.jpg)

​		local模式下，一个local进程只负责一个任务，用进程中的多个线程来模拟集群干活。local[4] 就是在开4个线程。上图中单词计数和学生分数统计 两个任务其实是开启了两个local模式下的进程，一共8个线程。



# 安装部署

​		在已经安装部署好了Hadoop和hive、mysql的基础上，我们来安装spark，但是这里我先安装一个anaconda来让后续工作更方便的开展。

```bash
# root 用户下
sh Anaconda3-2021.05-Linux-x86_64.sh
# 问我们要不要初始化init ，记得yes一下，不然默认no，另外就是anaconda的位置我们最好自己来定义一个。
```

​		修改anaconda的源为国内源。

```bash
# 重新进入root 用户，可以看见（base），显示已经有了anaconda。
vim ~/.condarc
# 这里用的是清华的源

channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch-lts: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```

​		创建一个python3.8的环境，名字叫pyspark。我的anaconda下python是3.8.8

```bash
conda create -n pyspark python=3.8
conda activate pyspark # 激活
conda deactivate 			 # 关闭
```



## local模式部署

​		local模式部署就在node01这一台机器上就行。

​		配置环境变量(python 用conda虚拟环境的)

```bash
vim /etc/profile
# 添加
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-1.el7_9.x86_64
export HADOOP_HOME=/opt/hadoop/hadoop
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export SPARk_HOME=/opt/spark/spark
export PYSPARK_PYTHON=/opt/anaconda3/envs/pyspark/bin/python3.8

vim /root/.bashrc 
# 添加
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-1.el7_9.x86_64
export PYSPARK_PYTHON=/opt/anaconda3/envs/pyspark/bin/python3.8

source /etc/profile
source /root/.bashrc
```

/opt/spark/spark/bin/pyspark 就是一个python解释器一样的东西，可以交互式运行python代码。

```bash
./pyspark
```

![image-20220329164750232](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/image-20220329164750232.png)

![image-20220329164812382](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/image-20220329164812382.png)

​		同理也可以用sparkr和sparkshell 用上r语言或者shell来运行spark。都用4040端口来监测，但是彼此独立。

```bash
./spark-submit --master local[*] /opt/spark/spark/examples/src/main/python/pi.py 10  # 试试运行案例
```

![截屏2022-03-29 17.01.05](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-29%2017.01.05.jpg)

driver就是master，4040端口占用就会申请4041以此类推。



## StandAlone模式部署

![截屏2022-03-30 11.44.50](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-30%2011.44.50.jpg)

![截屏2022-03-30 11.51.07](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-30%2011.51.07.jpg)

​		standalone模式下，有三种进程：master、worker、historyserver。

![截屏2022-03-30 11.50.48](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-30%2011.50.48.jpg)

​		值得注意的是，standalone模式下，不像local模式开启两个个spark任务，就有两个local进程；		standalone模式下，一个master和多个worded的框架是固定的，开启两个spark任务，只会在master进程中新增一个driver，同时在每一个worker中增加对应的executor，同一个spark任务的多个executor和与之对应的driver相通信。

​		注意：运行master的机器，也有一个worker在运行，也就是说，master机器也能执行任务。

### 前提准备

```bash
# 在master机器已经部署了local模式的前提下。
# 1\把anaconda 部署 到从属机器上，记得换清华源。
sh Anaconda3-2021.05-Linux-x86_64.sh
vim ~/.condarc

channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch-lts: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud

# 2\在从属机器建立python=3.8的conda环境。
conda create -n pyspark python=3.8

# 3\把spark安装好的文件夹从node01发送到从属机器node02中。
scp -r spark node02:/opt/

# 4\在从属机器中增加环境变量。
vim /etc/profile
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-1.el7_9.x86_64
export HADOOP_HOME=/opt/hadoop/hadoop
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export SPARk_HOME=/opt/spark/spark
export PYSPARK_PYTHON=/opt/anaconda3/envs/pyspark/bin/python3.8

vim /root/.bashrc 
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-1.el7_9.x86_64
export PYSPARK_PYTHON=/opt/anaconda3/envs/pyspark/bin/python3.8

source /etc/profile
source /root/.bashrc
```

### 修改配置文件

回到node01主机器

```bash
cd /opt/spark/spark/conf

# 指定worker所在的机器(workers文件)，包括主机器。
mv workers.template workers
vim workers
node01
node02
```

配置spark-env.sh文件

```bash
# 1. 改名
mv spark-env.sh.template spark-env.sh

# 2. 编辑spark-env.sh, 在底部追加如下内容
## 设置JAVA安装目录
JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-1.el7_9.x86_64

## HADOOP软件配置文件目录，读取HDFS上文件和运行YARN集群
HADOOP_CONF_DIR=/opt/hadoop/hadoop/etc/hadoop
YARN_CONF_DIR=/opt/hadoop/hadoop/etc/hadoop

## 指定spark老大Master的IP和提交任务的通信端口
# 告知Spark的master运行在哪个机器上
export SPARK_MASTER_HOST=node01
# 告知sparkmaster的通讯端口
export SPARK_MASTER_PORT=7077
# 告知spark master的 webui端口
SPARK_MASTER_WEBUI_PORT=8080

# worker cpu可用核数
SPARK_WORKER_CORES=1
# worker可用内存
SPARK_WORKER_MEMORY=1g
# worker的工作通讯地址
SPARK_WORKER_PORT=7078
# worker的 webui地址
SPARK_WORKER_WEBUI_PORT=8081

## 设置历史服务器
# 配置的意思是  将spark程序运行的历史日志 存到hdfs的/sparklog文件夹中 (先在自己的Hadoop配置文件etc/core-site.xml中查看自己的hdfs文件默认位置千万别写错了。)
export SPARK_HISTORY_OPTS="-Dspark.history.fs.logDirectory=hdfs://node01:9000/sparklog -Dspark.history.fs.cleaner.enabled=true"

```

指定了spark的日志文件位置后，需要新建这个文件。

```bash
# 启动hadoop后。
hadoop fs -ls / # 查看hdfs有哪些文件夹。
hadoop fs -mkdir /sparklog
hadoop fs -chmod 777 /sparklog
```

配置spark-defaults.conf文件

```shell 
# 1. 改名
mv spark-defaults.conf.template spark-defaults.conf
vim spark-defaults.conf
# 2. 修改内容, 追加如下内容
# 开启spark的日期记录功能
spark.eventLog.enabled 	true
# 设置spark日志记录的路径（先在自己的Hadoop配置文件etc/core-site.xml中查看自己的hdfs文件默认位置千万别写错了。）
spark.eventLog.dir	 hdfs://node01:9000/sparklog
# 设置spark日志是否启动压缩
spark.eventLog.compress 	true
```

配置log4j.properties 文件 

```shell
# 1. 改名
mv log4j.properties.template log4j.properties

# 将log4j.rootCategory=info 改为 log4j.rootCategory=warn
#这个文件的修改不是必须的,  为什么修改为WARN. 因为Spark是个话痨会疯狂输出日志, 设置级别为WARN 只输出警告和错误日志, 不要输出一堆废话.
```

在node01 和node02 分别设置好环境变量。

```shell
# node01 和 node02 都重复一遍。
vim /etc/profile
# 添加
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-1.el7_9.x86_64
export HADOOP_HOME=/opt/hadoop/hadoop
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export SPARk_HOME=/opt/spark/spark
export PYSPARK_PYTHON=/opt/anaconda3/envs/pyspark/bin/python3.8

vim /root/.bashrc 
# 添加
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-1.el7_9.x86_64
export PYSPARK_PYTHON=/opt/anaconda3/envs/pyspark/bin/python3.8

source /etc/profile
source /root/.bashrc
```

把node01配置好的spark文件发送一份到node02 中。

```shell
scp -r /opt/spark node02:/opt/
```

### 检验

验证历史日志进程是否打开。

```shell
# 先开启Hadoop
/opt/hadoop/hadoop/sbin/start-all.sh

# 再 开启spark
/opt/spark/spark/sbin/start-all.sh

# 开启日志
/opt/spark/spark/sbin/start-history-server.sh

# jps 监测java进程。
jps
```

![截屏2022-03-30 20.10.36](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-30%2020.10.36.jpg)

验证能不能在standalone模式下，用pyspark工具连接到spark集群去工作。

先看看spark集群的连接地址（spark://node01:7077）。![截屏2022-03-30 20.12.58](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-30%2020.12.58.jpg)

```shell
/opt/spark/spark/bin/pyspark  --master  spark://node01:7077
```

![截屏2022-03-30 20.15.52](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-30%2020.15.52.jpg)

可见其master不再指向local ，而是指向spark集群地址spark://node01:7077，同时有了一个任务id，在8080端口也能看见。

![截屏2022-03-30 20.18.47](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-30%2020.18.47.jpg)

​		

```shell
sc.parallelize([1,2,3,4]).map(lambda x:x*10).collect()
```

![截屏2022-03-30 20.24.33](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-30%2020.24.33.jpg)

注意这里的任务id 是被开启的pyspark程序的id，不是pyspark内运行的某个计算的id，8080端口可见。

![截屏2022-03-30 20.27.18](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-30%2020.27.18.jpg)

点击0001 id 可见

![截屏2022-03-30 20.28.15](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-30%2020.28.15.jpg)

也可以在     “Application Detail UI”  内查看pyspark完成计算的历史。实际是跳转到了node01:4040页面。

 ![截屏2022-03-30 20.33.37](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-30%2020.33.37.jpg)

点击executors，可见有一个driver，但是有两个executor在干活。

![截屏2022-03-30 20.34.30](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-30%2020.34.30.jpg)

和local不一样的地方是，standalone模式下，把一个pyspark工具退出了，其实是把一个driver退出了(driver只有在任务运行时才存在，默认4040端口监督) ，但是master和worker还在，所以4040端口打不开了，8080端口依然可见，原理可以看 “ StandAlone模式部署” 的图示。

提交程序到spark集群中去运行

```shell
/opt/spark/spark/bin/spark-submit --master spark://node01:7077 /opt/spark/spark/examples/src/main/python/pi.py  100
```

运行完毕后，可见4040端口打不开，因为任务已经结束，driver没了。8080端口可见其任务运行记录。

![截屏2022-03-30 20.51.48](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-30%2020.51.48.jpg)

在18080端口查看history，实际上显示的和4040一样，4040端口随着driver的关闭而失效了，我们可以用18080端口看历史。

![截屏2022-03-30 20.55.20](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-30%2020.55.20.jpg)

一直点击，可以看见底层的task是交给多个worker来执行的。

![截屏2022-03-30 21.00.53](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-30%2021.00.53.jpg)



# spark运行的层次结构

![截屏2022-03-31 17.25.34](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-31%2017.25.34.jpg)

driver是某个任务程序的管理者，master是集群的管理者。

![截屏2022-03-31 18.04.43](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-31%2018.04.43.jpg)

![截屏2022-03-31 18.06.41](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-31%2018.06.41.jpg)

![截屏2022-03-31 18.09.07](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-31%2018.09.07.jpg)

![截屏2022-03-31 18.10.32](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-31%2018.10.32.jpg)

# StandAlone HA环境搭建

​		主从结构天然有单点故障的风险。master一旦出问题集群就完蛋。



![截屏2022-03-31 18.18.37](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-31%2018.18.37.jpg)

![截屏2022-03-31 18.30.52](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-31%2018.30.52.jpg)

## 安装zookeeper集群

​		![截屏2022-03-31 23.22.19](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-03-31%2023.22.19.jpg)

​		我们这里直接用zookeeper的集群配置，zookeeper这么个玩样儿是让节点死的时候，其它节点通过3888选举端口实现选举机制，选一个节点顶上死掉的节点，让集群正常工作。

```shell
# 下载zookeeper，解压、改文件夹名为zookeeper放在/opt下。
 http://zookeeper.apache.org/
 
# 关闭防火墙，保持所有端口开放，其实如果开了防火墙，保证2888 3888 等等几个端口开放就行。

# <改环境变量>
vim /etc/profile
export ZOOKEEPER_HOME=/opt/zookeeper
export PATH=.:${JAVA_HOME}/bin:${SCALA_HOME}/bin:${SPARK_HOME}/bin:${HADOOP_HOME}/bin:${ZK_HOME}/bin:${HBASE_HOME}/bin:${HIVE_HOME}/bin:$PATH:$ZOOKEEPER_HOME/bin

# node01机器上配置zookeeper
cd /opt/zookeeper
mkdir data 
cd /opt/zookeeper/conf 
cp zoo_sample.cfg zoo.cfg
vim zoo.cfg
admin.serverPort=8989 # 我没使用默认的 8080 端口，而是用了8989端口。怕冲突。
maxClientCnxns=120    # 最大客户端数设定为120
dataDir=/opt/zookeeper/data
server.1=node01:2888:3888 # 因为之前设置了/etc/hosts,用node01代替IP,1号机<唯一标识1>。
server.2=node02:2888:3888
server.3=node03:2888:3888

# 分发给其他节点。
scp -r /opt/zookeeper node02:/opt/
scp -r /opt/zookeeper node03:/opt/

# 其他节点<改环境变量>，同上。

# node01、node02、node03 各自创建myid文件，并写入唯一标识。
cd /opt/zookeeper/data
touch myid
echo "1">>myid # 1号机<唯一标识1> 2号机<唯一标识2> echo "2">>myid依次类推。

# 在每一台机器上都开启zookeeper服务。
/opt/zookeeper/bin/zkServer.sh    start

# 在每台机器上检查状态
/opt/zookeeper/bin/zkServer.sh    status

# 在每台机器关闭zookeeper服务。
/opt/zookeeper/bin/zkServer.sh    stop
```

![截屏2022-04-01 00.26.01](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-01%2000.26.01.jpg)

​		可见，Hadoop和spark中 设定为master的node01机器，在zookeeper中并不是leader，只是follower，而leader是node02机器。

## StandAlone HA

​		在开启了zookeeper服务和hdfs服务前提下。

​		先在`spark-env.sh`中, 注释掉: `SPARK_MASTER_HOST=node1`


原因: 配置文件中固定master是谁, 那么就无法用到zk的动态切换master功能了.


​		在`spark-env.sh`中, 增加:


```shell
SPARK_DAEMON_JAVA_OPTS="-Dspark.deploy.recoveryMode=ZOOKEEPER -Dspark.deploy.zookeeper.url=node01:2181,node02:2181,node03:2181 -Dspark.deploy.zookeeper.dir=/spark-ha"
# spark.deploy.recoveryMode 指定HA模式 基于Zookeeper实现
# 指定Zookeeper的连接地址
# 指定在Zookeeper中注册临时节点的路径
```


将spark-env.sh 分发到每一台服务器上


```shell
scp spark-env.sh node02:/opt/spark/spark/conf/
scp spark-env.sh node03:/opt/spark/spark/conf/
```


停止当前StandAlone集群停止当前spark的StandAlone集群


```shell
sbin/stop-all.sh
```


启动集群:

```shell
# 在node1上 启动一个master 和全部worker,node01机器抢到了master（alive状态）的8080端口。
sbin/start-all.sh

# 注意, 下面命令在node2上执行
sbin/start-master.sh
# 在node2上启动一个备用的master进程。 但是这个node02上的master（standby状态），在node01的master抢占了8080端口，而配套的三个worker抢占了8081端口，他就只能抢占8082端口了。
```

![截屏2022-04-01 18.10.44](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-01%2018.10.44.jpg)

​		可见node02上的master其实占用的是8082端口，我们在node02:8082 查看，可见其状态在standby等待中。

![截屏2022-04-01 18.13.03](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-01%2018.13.03.jpg)

### master主备切换

​		在node01、node02、node03任意机器提交一个spark任务到当前`alive`master上，让node01领导的worker集群去处理（spark-subimit   --master   集群地址为***    文件   参数）:


```shell
bin/spark-submit --master spark://node01:7077 /opt/spark/spark/examples/src/main/python/pi.py 1000
```

在提交成功后, 程序还在运行的过程中，突然将alivemaster  kill掉 

 `kill -9 node01中jps看见的master进程号`


不会影响程序运行:

![截屏2022-04-01 18.16.22](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-01%2018.16.22.jpg)

当新的master接收集群后, 程序继续运行, 正常得到结果.

我们在node02:8082端口 可见，node02的master已经从standby转为了alive状态（我猜测worker们现在占用的变成了8083端口，8081端口被空出来了）。

![截屏2022-04-01 18.18.04](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-01%2018.18.04.jpg)

此时，node01的master没有了，三个worker又只有了一个master，有单点故障的风险，所以我们应该重新在node01机器上 开启一个master服务。

node01  执行 `sbin/start-master.sh`

不过node01重新开启master后，换成了8081的端口，而且变成了standby等待状态，此时node02的master占用8082端口，是alive状态。

![截屏2022-04-01 18.25.26](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-01%2018.25.26.jpg)


> 结论 HA模式下, 主备切换 不会影响到正在运行的程序.
>
> 最大的影响是 会让它中断大约30秒左右.

![截屏2022-04-01 18.34.36](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-01%2018.34.36.jpg)



# Spark on Yarn 环境搭建

![截屏2022-04-01 18.36.59](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-01%2018.36.59.jpg)

![截屏2022-04-01 18.38.25](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-01%2018.38.25.jpg)

![截屏2022-04-01 18.46.58](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-01%2018.46.58.jpg)

​		spark 仅仅保留driver和executor运行的能力，资源管理交给Hadoop自带的 yarn即可。避免了master、worker结构和yarn已有的结构重叠浪费资源和冲突的问题。

​		其实仅仅在spark-env.sh中配置有Hadoop的conf和yarn的conf环境变量，就能用spark on yarn了，炒鸡简单。

```shell
## HADOOP软件配置文件目录，读取HDFS上文件和运行YARN集群
HADOOP_CONF_DIR=/opt/hadoop/hadoop/etc/hadoop
YARN_CONF_DIR=/opt/hadoop/hadoop/etc/hadoop
```

```shell
# 提交运行 例子
bin/spark-submit --master yarn /opt/spark/spark/examples/src/main/python/pi.py 1000
# 交互式运行
bin/pyspark --master yarn 
```

可以在yarn的node01:8088端口看到pyspark的脚本运行。

![截屏2022-04-01 19.05.17](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-01%2019.05.17.jpg)



## spark on yarn 的两种运行模式

![截屏2022-04-01 19.07.44](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-01%2019.07.44.jpg)

![截屏2022-04-02 16.03.57](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-02%2016.03.57.jpg)

![截屏2022-04-02 16.04.19](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-02%2016.04.19.jpg)

​		cluster模式是yarn内部通信（driver和executor），client模式是yarn内部的executor和yarn外部的driver通信。性能当然cluster好，但是交互式体验明显client更好。

![截屏2022-04-02 17.03.38](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-02%2017.03.38.jpg)

```shell
# 指定为spark-yarn客户端模式，其实默认也就是客户端模式。
bin/spark-submit --master yarn --deploy-mode client  /opt/spark/spark/examples/src/main/python/pi.py 100
```

​		在开启spark的history服务后，选中18080端口此任务的executor，可见driver没有生成logs文件。

![截屏2022-04-02 18.08.32](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-02%2018.08.32.jpg)

![截屏2022-04-02 18.08.58](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-02%2018.08.58.jpg)

​		这是由于driver在客户端内，logs信息都生成在命令窗口了，如下。

![截屏2022-04-02 18.10.43](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-02%2018.10.43.jpg)

​		

```shell
# 指定为spark-yarn集群模式cluster，driver在datanode的容器内，集成进了yarn。
bin/spark-submit --master yarn --deploy-mode cluster  /opt/spark/spark/examples/src/main/python/pi.py 100
```

​		在开启spark的history服务后，选中18080端口此任务的executor，可见driver生成了logs文件，并且命令窗口没有输出。



![截屏2022-04-02 21.48.22](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-02%2021.48.22.jpg)

![截屏2022-04-02 21.51.25](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-02%2021.51.25.jpg)

​		

​		两种模式都是先生成application master，（但是一个driver在外一个driver在内）然后applicationmaster和driver通信问问要啥，application master回头再去和resourcemanager要资源，告诉nodemanager兄弟们启动executor，executor再去找driver 反向注册，形成一套driver和executors的计算框架。

![截屏2022-04-02 22.03.48](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-02%2022.03.48.jpg)



# 框架和类库

![截屏2022-04-02 22.06.40](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-02%2022.06.40.jpg)

![截屏2022-04-02 22.10.51](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-02%2022.10.51.jpg)

![截屏2022-04-02 22.15.17](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-02%2022.15.17.jpg)

```shell 
# 每一台机器都执行一遍
conda activate pyspark 
pip install pyspark -i https://pypi.tuna.tsinghua.edu.cn/simple
```



# 基础实战

![截屏2022-04-02 22.26.08](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-02%2022.26.08.jpg)

​		Windows要打Hadoop补丁，macOS和Linux不用这么麻烦，直接跳过。	

​		因为我在centos的虚拟机内之前已经安装好了idea，所以就不按照pycharm了，直接在idea添加python插件再添加conda虚拟环境中python的编译器就好。

​		添加idea的python插件。

​		![截屏2022-04-02 22.47.21](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-02%2022.47.21.jpg)

​		这里顺带提一句题外话，idea的永久激活工具在cache 缓存中有文件，自己为了centos精简空间清理了cache缓存可能让idea的激活失败，所以还是少清理cache。

​		添加之前conda虚拟环境建好的python解释器。

![截屏2022-04-03 10.36.30](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-03%2010.36.30.jpg)

​		当然，我其实没有按照最优的方法去配置，最优的方法是在mac上运行pycharm或者idea，然后在python解释器配置中，配置一个ssh远程连接到虚拟机的centos的conda虚拟环境的python解释器，这样运行是最好的，我猜工作中用的也最多。就点`conda environment `下面的`ssh interpreter`配置就行了。配置好远程解释器之后，project的文件夹会和虚拟机的相应文件夹同步。



![截屏2022-04-03 11.40.18](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-03%2011.40.18.jpg)



![截屏2022-04-03 17.04.40](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-03%2017.04.40.jpg)

![截屏2022-04-03 17.18.23](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-03%2017.18.23.jpg)

以上代码要注意两个点：

​		1、最好不要指定`setMaster()`, 如果要在spark-submit 提交给spark on yarn运行，代码`setMaster("local[*]")`设置了本地模式，本地模式会和集群模式冲突；

​		2、最好用hdfs的文件，而不是本地文件，因为要用到集群计算，别的机器可能找不到文件位置，hdfs大家都能访问的到。	

```shell
vim /opt/data/idea_pyspark/test.py
```

```python
# coding:utf8
from pyspark import SparkConf ,SparkContext
if __name__=='__main__':
    conf = SparkConf().setAppName("wordcounthelloworld")
    # local model
    sc = SparkContext(conf=conf)

    file_rdd = sc.textFile("hdfs://node01:9000/tmp/words") # local is also ok
    words_rdd = file_rdd.flatMap(lambda line:line.split(" "))
    words_with_one_rdd = words_rdd.map(lambda x: (x, 1))
    result_rdd = words_with_one_rdd.reduceByKey(lambda a, b: a + b)

    print(result_rdd.collect())
```

```shell
# 在spark on yarn 的客户端模式，运行自己写的脚本。需要一点时间来构建容器等等前面说过的步骤。
spark-submit --master yarn /opt/data/idea_pyspark/test.py
```

![截屏2022-04-03 17.44.13](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-03%2017.44.13.jpg)

​		

# spark架构原理回顾（基于python）

![截屏2022-04-03 18.13.33](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-03%2018.13.33.jpg)

![截屏2022-04-03 18.15.43](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-03%2018.15.43.jpg)

![截屏2022-04-03 18.21.47](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-03%2018.21.47.jpg)

​		上图简单来说，driver是python翻译成java给jvm driver，但是executor中jvm executor仅仅是传达指令，工作用python executor来做。

​		![截屏2022-04-03 18.25.04](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-03%2018.25.04.jpg)

![截屏2022-04-03 18.26.59](spark%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E5%8F%8A%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2.assets/%E6%88%AA%E5%B1%8F2022-04-03%2018.26.59.jpg)

