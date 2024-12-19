---
layout: posts
title: centos7安装Hive
date: 2022-10-29 14:41:49
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "hive"
tags:
- "centos"
- "hive"
---


		安装hive，需要先安装配置好Hadoop，可以看之前的Hadoop安装配置笔记。先启动Hadoop，接下来的操作都是在主节点中进行，我的主节点机器主机名是node01。
<!--more-->
# 一、将hive解压，并在Hadoop的core-site.xml中整合hive。 

```bash
tar -zxvf apache-hive-3.1.2-bin.tar.gz 
vim /opt/hadoop/hadoop/etc/hadoop/core-site.xml
```

```bash
# 追加如下两个属性； Hadoop集群同步配置文件，重启生效(我允许* 就是任何用户任何分组，用root用户权限来运行，所以下面是root.hosts\root.groups)。
        <!--整合 prepare for hive -->
        <property>
                <name>hadoop.proxyuser.root.hosts</name>
                <value>*</value>
        </property>
        <property>
                <name>hadoop.proxyuser.root.groups</name>
                <value>*</value>
        </property>

```



## 二、检查Hadoop和hive的guava版本是否有差异。

```bash
# 如果Hadoop和hive的guava版本有差异，就用Hadoop下的guava包替换hive的guava包。
cd /opt/hadoop/hadoop/share/hadoop/common/lib
ls | grep guava
# 显示： guava-27.0-jre.jar

cd /opt/hive/apache-hive-3.1.2-bin/lib
ls | grep guava
# 显示： guava-19.0.jar

# 可见Hadoop的guava版本高一些，所以用Hadoop的guava覆盖hive的guava。
cp /opt/hadoop/hadoop/share/hadoop/common/lib/guava-27.0-jre.jar  /opt/hive/apache-hive-3.1.2-bin/lib

rm -rf /opt/hive/apache-hive-3.1.2-bin/lib/guava-19.0.jar
```



## 三、添加数据驱动包

​		将mysql的java驱动包     *   上传到  /opt/hive/apache-hive-3.1.2-bin/lib 。	

​		驱动包下载地址：https://mvnrepository.com/artifact/mysql/mysql-connector-java![截屏2022-02-27 15.56.42](centos7%E5%AE%89%E8%A3%85Hive.assets/%E6%88%AA%E5%B1%8F2022-02-27%2015.56.42-7026054.jpg)

```bash
# 我的mysql版本  Server version: 5.7.26 MySQL 
# mysql官方推荐mysql5.6以上 使用connector/j 8.0 ，而且在使用时需要对时区进行设置。java推荐java1.8以后都用connector/j 8.0 。
cp mysql-connector-java-8.0.25.jar /opt/hive/apache-hive-3.1.2-bin/lib # 或者用finalshell直接传进hive的lib中。
```

![截屏2022-02-27 16.43.28](centos7%E5%AE%89%E8%A3%85Hive.assets/%E6%88%AA%E5%B1%8F2022-02-27%2016.43.28.jpg)



# 四、添加环境变量

```bash
vim /etc/profile
# 当然啦，也可以和我配置Hadoop一样在 /root/.bash_profile 中配置环境变量，就是那样需要每次都source /root/.bash_profile 激活一次，有点小麻烦。现在我们换个口味。
```

```bash
# {} 加不加都可以。这里试试加花括号效果一样。
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-1.el7_9.x86_64
export HIVE_HOME=/opt/hive/apache-hive-3.1.2-bin
export HIVE_CONF_DIR=${HIVE_HOME}/conf
export HIVE_AUX_JARS_PATH=/opt/hive/apache-hive-3.1.2-bin/lib
export PATH=.:${JAVA_HOME}/bin:${SCALA_HOME}/bin:${SPARK_HOME}/bin:${HADOOP_HOME}/bin:${ZK_HOME}/bin:${HBASE_HOME}/bin:${HIVE_HOME}/bin:$PATH
```

```bash
source /etc/profile # 生效
```



# 五、在conf中更改配置

## 1、新建hive-site.xml(远程模式部署metastore服务 ，链接上mysql的hive数据库。)

```bash
vim /opt/hive/apache-hive-3.1.2-bin/conf/hive-site.xml
```

```bash
<configuration>
  <property>
    <!--mysql 搭建在第一台机器上，用到的database名字就叫hive(这个是定好的)。-->
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://node01:3306/hive?createDatabaseIfNotExist=true&amp;useSSL=false</value>
  </property>
  <property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.jdbc.Driver</value>
  </property>
  
  <!--hive中要用到mysql，这里填写 mysql的用户名。我先就在mysql中创建了一个名叫hive的用户。 -->
  <property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>hive</value>
  </property>
  
  <!--上面mysql用户名对应的 mysql数据库登录密码（需要修改成密码） -->
  <property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>hive</value>
  </property>

  <!--远程模式部署metastore服务地址,hive就安装在node01上，用thrift协议对外，端口9083 -->
  <property>
    <name>hive.metastore.uris</name>
    <value>thrift://node01:9083</value>
  </property>
  
  <!--关闭元数据存储授权-->
  <property>
    <name>hive.metastore.event.db.notification.api.auth</name>
    <value>false</value>
  </property>
  <!--关闭元数据存储版本验证-->
  <property>
    <name>hive.metastore.schema.verification</name>
    <value>false</value>
  </property>
</configuration>
```

## 2、修改hive-env.sh

```bash
cp hive-env.sh.template hive-env.sh
vim hive-env.sh
```

```bash
export HADOOP_HOME=/opt/hadoop/hadoop
export HIVE_CONF_DIR=/opt/hive/apache-hive-3.1.2-bin/conf
export HIVE_AUX_JARS_PATH=/opt/hive/apache-hive-3.1.2-bin/lib
```



# 六、初始化metadata

```bash
# 在hive 的bin中初始化metadata，指定元数据在mysql当中。
cd /opt/hive/apache-hive-3.1.2-bin/bin
schematool -initSchema -dbType mysql -verbos
```

判断是否初始化metadata成功的方法，就是去mysql看看有没有新生成一个叫hive的database，同时hive 的database中是否有74张表。![截屏2022-02-27 17.25.21](centos7%E5%AE%89%E8%A3%85Hive.assets/%E6%88%AA%E5%B1%8F2022-02-27%2017.25.21.jpg)

![截屏2022-02-27 17.37.44](centos7%E5%AE%89%E8%A3%85Hive.assets/%E6%88%AA%E5%B1%8F2022-02-27%2017.37.44.jpg)

​		但是此时直接在bin运行 hive的命令会报错

```bash
cd /opt/hive/apache-hive-3.1.2-bin/bin
hive # 进入hive 输命令的界面
show databases;
# 报错  Unable to instantiate org.apache.hadoop.hive.ql.metadata.SessionHiveMetaStoreClient 无法实例化metastore的客户端。
```

![截屏2022-02-27 17.45.14](centos7%E5%AE%89%E8%A3%85Hive.assets/%E6%88%AA%E5%B1%8F2022-02-27%2017.45.14.jpg)

​		从上图可知报错原因，要先手动打开metastore服务。不然直接启动hive，链接不到mysql中的元数据。



# 七、手动开启metastore服务（多种方式可选）

```bash
# 任选一种：
# 1、前台启动（一直要挂着一个命令窗口）
cd /opt/hive/apache-hive-3.1.2-bin/bin
hive --service metastore

# 2、放在后台以进程方式启动。
nohup hive --service metastore &
    # 回车
jps # 可以看见下面多了一个Runjar
ll  # 可以看见多了一个 nohup.out 文件,存放日志文件。
		# 但 这样的后台进程方式启动的服务，想要删除只能 jps 找到对应的进程号 再 “kill -9 进程号” 删除，不能ctrl+c这样结束。

# 3、前台启动，加上debug日志
hive --service metastore --hiveconf hive.root.logger=DEBUG,console
```

```bash
hive
show databases; # 不再报错。
```



# 八、在从属机器使用hive 和beeline 客户端

​		在node02机器上重复上面操作,或者node01直接发送到node02。

```bash
# node01直接ssh发送过去。
cd /opt
scp -r /opt/hive node02:/opt
```

```bash
# 注意： hive-site.xml配置 仅仅需要保留 访问metastore的能力就够了。
vim /opt/hive/apache-hive-3.1.2-bin/conf/hive-site.xml
```

```bash
<configuration>
    <!--远程模式部署metastore服务地址,hive就安装在node01上，用thrift协议对外，端口9083 -->
  <property>
    <name>hive.metastore.uris</name>
    <value>thrift://node01:9083</value>
  </property>

# 以下部分都不用。
<!--
  <property>
    <!--mysql 搭建在第一台机器上，用到的database名字就叫hive(这个是定好的)。-->
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://node01:3306/hive?createDatabaseIfNotExist=true&amp;useSSL=false</value>
  </property>
  <property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.jdbc.Driver</value>
  </property>
  
  <!--hive中要用到mysql，这里填写 mysql的用户名。我先就在mysql中创建了一个名叫hive的用户。 -->
  <property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>hive</value>
  </property>
  
  <!--上面mysql用户名对应的 mysql数据库登录密码（需要修改成密码） -->
  <property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>hive</value>
  </property>
  
  <!--关闭元数据存储授权-->
  <property>
    <name>hive.metastore.event.db.notification.api.auth</name>
    <value>false</value>
  </property>
  <!--关闭元数据存储版本验证-->
  <property>
    <name>hive.metastore.schema.verification</name>
    <value>false</value>
  </property>
-->
</configuration>
```

​		node02 再个填环境变量。

```bash
vim /etc/profile

# {} 加不加都可以。这里试试加花括号效果一样。
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-1.el7_9.x86_64
export HIVE_HOME=/opt/hive/apache-hive-3.1.2-bin
export HIVE_CONF_DIR=${HIVE_HOME}/conf
export HIVE_AUX_JARS_PATH=/opt/hive/apache-hive-3.1.2-bin/lib
export PATH=.:${JAVA_HOME}/bin:${SCALA_HOME}/bin:${SPARK_HOME}/bin:${HADOOP_HOME}/bin:${ZK_HOME}/bin:${HBASE_HOME}/bin:${HIVE_HOME}/bin:$PATH

source /etc/profile 
```

## 	

## 	1、hive

​		操作同上。

## 	2、beeline

​				![截屏2022-02-27 17.45.14](centos7%E5%AE%89%E8%A3%85Hive.assets/%E6%88%AA%E5%B1%8F2022-02-27%2017.45.14.jpg)

开启beeline前要先在主机器node01 打开 metastore、hiveserver2 两个服务。

```bash
/opt/hive/apache-hive-3.1.2-bin/bin/hive --service metastore
/opt/hive/apache-hive-3.1.2-bin/bin/hive --service hiveserver2
```

node02 开启beeline客户端,并远程登录到node01 的hiveserver2。

```bash
/opt/hive/apache-hive-3.1.2-bin/bin/beeline
! connect jdbc:hive2://node01:10000
# 然后用root登录，不用密码直接回车。
```

![截屏2022-02-27 20.43.36](centos7%E5%AE%89%E8%A3%85Hive.assets/%E6%88%AA%E5%B1%8F2022-02-27%2020.43.36.jpg)

![截屏2022-02-27 20.44.10](centos7%E5%AE%89%E8%A3%85Hive.assets/%E6%88%AA%E5%B1%8F2022-02-27%2020.44.10.jpg)

beeline界面确实是要漂亮一些。


