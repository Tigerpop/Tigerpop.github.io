---
layout: posts
title: centos7安装Hadoop
date: 2022-10-29 14:44:42
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "hive"
tags:
- "centos"
- "hadoop"
---

先下载 <!--more-->
# 一、去各自官网先把东西都下载好 

<img src="centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-25%2014.55.36-7026137.jpg" alt="截屏2022-02-25 14.55.36" style="zoom:25%;" />

​		centos系统相关的安装部署可以参考之前Linux学习的笔记，什么固定虚拟机IP啦，安装mysql啦，远程文件传输啦，这里就不重复说了。

​		把这些工具下载好，然后在/opt 下新建hadoop文件夹、spark文件夹等等，把对应的压缩包放进去。



# 二、安装比较新的java：![截屏2022-02-25 15.06.40](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-25%2015.06.40.jpg)



# 三、复制一个虚拟机

（先关闭vmware程序，再把vmwarevm文件夹复制一个就好了，里面什么东西都是一样的，双击vmwarevm文件夹打开就能运行。）

![截屏2022-02-25 15.37.18](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-25%2015.37.18.jpg)

​		记得把不同机器的IP改一下，固定在同一网段不同的IP上。之前我固定的虚拟机的网关是192.168.200.2，node1机器固定的IP是192.168.200.100。记得把node2改为192.168.200.101。

```bash
# 修改固定ip，参考之前Linux学习固定IP的内容，这里仅仅需要改动一下ip即可，别的网关什么的都不用动。
vim /etc/sysconfig/network-scripts/ifcfg-ens33
```

![截屏2022-02-25 16.05.19](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-25%2016.05.19-7026137.jpg)



# 四、修改主机名和hosts文件

```bash
# 最好设置成node01、node02这样以后好区分，甚至是用户名也设置为node01、node02 以后容易区分。
su
vim /etc/hostname
vim /etc/hosts
```

![截屏2022-02-25 16.12.11](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-25%2016.12.11.jpg)



# 五、虚拟机之间设置ssh无密码登录，同时关闭防火墙。

​		机器关闭防火墙（不然后面hadoop启动了找不到结点）：

```bash
# 每个机器都操作一遍。
systemctl stop firewalld     # 关闭防火墙
systemctl disable firewalld  # 关闭防火墙的开机自启动。 
```

​		相互把公钥放在对面对应存公钥的位置。

补充知识点：

![截屏2022-02-25 16.35.13](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-25%2016.35.13.jpg)

![截屏2022-02-25 16.35.38](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-25%2016.35.38.jpg)

```bash
# 先两台机器相互ssh登录一下对方。再如下操作。
#[root@node01 .ssh]#
ssh-keygen -t dsa -P ''

#[root@node01 .ssh]# 把一号机的公钥放进公钥池子里。
cat id_dsa.pub >> authorized_keys

#[root@node01 .ssh]# 一号机把公钥池内容 追加到二号机公钥池 。
scp authorized_keys root@node02:/root/.ssh/authorized_keys

#[root@node02 .ssh]# 把二号机的公钥放进二号机公钥池子里。（此时二号机公钥池已经有了一号机、二号机的公钥了。）
cat id_dsa.pub >> authorized_keys

#[root@node02 .ssh]# 二号机把公钥池内容 追加到一号机公钥池 。此时一号机公钥池也已经有了一号机、二号机的公钥了。）
scp authorized_keys root@node01:/root/.ssh/authorized_keys


# 以后在root权限下 
# <ssh 用户名@hosts> 我这里已经把hosts改成了 node01 和 node02了。
# 或者直接
# ssh node01 、 ssh node02 
# 登录其它虚拟机就不用输入对方root密码了，进去了就是root用户。  

# 如果有多台虚拟机，以此类推即可。
```





# 六、在node01安装Hadoop 

## 1、配置java和Hadoop环境



```bash
# 找java位置。
whereis java
# java: /usr/bin/java /usr/lib/java /etc/java /usr/share/java /usr/share/man/man1/java.1.gz
```

因为是yum安装的java，我们先whereis，再不断了 ll 软连接,溯源找到java实体文件，不再是软连接时，结合这个地址可以分析得到, JAVA_HOME 的值应该就是jre前面这一截。

![截屏2022-02-25 20.34.42](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-25%2020.34.42.jpg)

```bash
# 这个就应该被设置为环境变量中的  JAVA_HOME 。
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-1.el7_9.x86_64
```

运行[javac](https://so.csdn.net/so/search?q=javac&spm=1001.2101.3001.7020)命令报错：bash:javac:command not found；

最后发现自带的[jdk](https://so.csdn.net/so/search?q=jdk&spm=1001.2101.3001.7020)是没有安装开发环境的，需要安装devel才有javac命令。

（找到jdk的安装目录下，发现只有[jre](https://so.csdn.net/so/search?q=jre&spm=1001.2101.3001.7020)文件夹，没有bin、lib等文件夹）。

**解决方法**：使用[yum](https://so.csdn.net/so/search?q=yum&spm=1001.2101.3001.7020)安装开发环境 devel。（注意下载对应的版本。所以最好用yum找到准确的devel版本名字，精准下载）

用yum查找可以安装的jdk：yum search java|grep jdk

![截屏2022-02-25 21.14.09](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-25%2021.14.09.jpg)

```bash
yum install java-1.8.0-openjdk-devel.x86_64
# 下载了开发环境后，再看看 javac 命令不会报错了。同时，到/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-1.el7_9.x86_64文件夹下，可以看见出了jre 又多了bin 等等一些文件夹了。
```

![截屏2022-02-25 21.17.46](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-25%2021.17.46.jpg)

```bash
vim /root/.bash_profile # 添加环境

export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-1.el7_9.x86_64
export PATH=.:$JAVA_HOME:$JAVA_HOME/bin:$PATH

export HADOOP_HOME=/opt/hadoop/hadoop
export PATH=.:$JAVA_HOME:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH

source /root/.bash_profile # 生效

# 此时 可以 检查hadoop是否安装好了。
hadoop version
```





-----------------------------------------------------------------------------

## （后续综述）

配置文件目录： /opt/hadoop/hadoop/etc/hadoop   需要修改的配置文件：hadoop-env.sh，core-site.xml， hdfs-site.xml ，（配置mapreduce）yarn-site.xml，mapred-site.xml ，workers



-----------------------------------------------------------------------------

## 2、配置hadoop-env.sh（目的是绑定java和Hadoop）

```bash
vim /opt/hadoop/hadoop/etc/hadoop/hadoop-env.sh
# 配置 jdk的home路径
# 绑定java和hadoop的原因是centos有一些更新不允许Hadoop去访问系统的配置。
```

```bash
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-1.el7_9.x86_64

export HDFS_NAMENODE_USER=root
export HDFS_DATANODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
export YARN_RESOURCEMANAGER_USER=root
export YARN_NODEMANAGER_USER=root

export HADOOP_PID_DIR=/opt/hadoop/hadoop/data/pids
export HADOOP_LOG_DIR=/opt/hadoop/hadoop/data/logs
```

## 3、配置core-site.xml（Hadoop核心文件配置） 

```bash
vim /opt/hadoop/hadoop/etc/hadoop/core-site.xml
# 核心配置文件，例如HDFS、MapReduce和YARN查用的I/O设置
```

```bash
<configuration>
		<!--指定文件系统的入口地址，可以为主机名或IP -->
		<!--端口默认8020 -->
		<property>　　
				<name>fs.defaultFS</name>　　
				<value>hdfs://node01:9000</value>
		</property>
		<!--HDFS的存储目录,也就是Hadoop临时工作存放目录-->
		<property>
				<name>hadoop.tmp.dir</name>
				<value>/opt/hadoop/hadoop/tmp</value>
		</property>
</configuration>
```

![截屏2022-02-26 12.10.56](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-26%2012.10.56.jpg)

## 4、配置yarn-env.sh

```bash
vim /opt/hadoop/hadoop/etc/hadoop/yarn-env.sh
```

```bash
# 把java环境再搞上去。
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-1.el7_9.x86_64
```

## 5、配置hdfs-site.xml (hdfs存储配置)

```bash
vim /opt/hadoop/hadoop/etc/hadoop/hdfs-site.xml
# 布式文件系统的核心配置 决定了数据存放在哪个路径，数据的副本，数据的block块大小等等
```

```bash
<configuration>
<!--指定hdfs备份数量，小于等于从节点数目,就是从属设备的数量。 -->
<property>
        <name>dfs.replication</name>
        <value>1</value>
</property>

<!--自定义hdfs的namenode存储位置。 -->
<property>
        <name>dfs.namenode.name.dir</name>
        <value>file:/opt/hadoop/hadoop/data/name</value>
</property>
<!--自定义hdfs的datanode存储位置。 -->
<property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/opt/hadoop/hadoop/data/data</value>
</property>

<property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
</property>

<!--注意Hadoop3以后端口由50070改为了9870 -->
<property>
        # <name>dfs.namenode.secondary.http-address</name>
        # 这里好危险，我找logs日志才发现这里写错了。
        <name>dfs.namenode.http-address</name>
        <value>node01:9870</value>
</property>

</configuration>
```

## 6、配置mapred-site.xml

```bash
vim /opt/hadoop/hadoop/etc/hadoop/mapred-site.xml
```

```bash
<configuration>
<property>
    <name>mapred.job.tracker</name>
    <value>node01:9001</value>
</property>

<!--hadoop的mapreduce程序运行在yarn上,默认值为local -->
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>

<property>  
    <name>mapreduce.application.classpath</name>  
    <value>/opt/hadoop/hadoop/share/hadoop/mapreduce/*, /opt/hadoop/hadoop/share/hadoop/mapreduce/lib/*</value>
</property>
</configuration>
```

## 7、配置yarn-site.xml(定义yarn集群)

```bash
vim /opt/hadoop/hadoop/etc/hadoop/yarn-site.xml
```

```bash
<configuration>
<property>
       <name>yarn.nodemanager.aux-services</name>
       <value>mapreduce_shuffle</value>
   </property>
   <property>
       <name>yarn.resourcemanager.hostname</name>
       <value>node01</value>
</property>
</configuration>
```

## 8、配置workers

```bash
vim /opt/hadoop/hadoop/etc/hadoop/workers
# 把所有从节点的主机名写到这儿就可以，这是告诉hadoop进程哪些机器是从节点 每行写一个
```

```bash
node02 # 我就设置了一个从节点。
```

## 9、将Hadoop文件发送到其它节点

```bash
# scp 是将文件复制到 用ssh登录机器的指定位置。我每个虚拟机的/opt下的第一个hadoop文件夹都是无意义的自己多建了一层。（时间可能要几分钟，喝杯牛奶等一等。）
scp -r /opt/hadoop/hadoop  node02:/opt/hadoop
```



# 七、初始化、启动、验证Hadoop。

```bash
cd /opt/hadoop/hadoop/bin
# 到主机器（我这机器名是node01）Hadoop根目录的bin中 运行如下初始化命令。
hdfs namenode -format

cd /opt/hadoop/hadoop/sbin

# 到主机器（我这机器名是node01）sbin中 运行
# 由于我之前的环境变量都是在 /root/.bash)profile 中，所以每次启动前都要让这个环境变量生效一次。
source /root/.bash_profile
start-dfs.sh
start-yarn.sh
```

![截屏2022-02-26 17.57.46](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-26%2017.57.46.jpg)

细心的看看可以发现我前面有个小问题，因为我复制出node02时，node02继承了node01的java，但是那时node01还没有装devel开发环境，我们在node02机器javac会说找不到此命令，因此我回到 《六-1、配置java和Hadoop环境》在node02机器重复一遍

```bash
# 进入node02机器。
yum install java-1.8.0-openjdk-devel.x86_64

vim /root/.bash_profile # 添加环境

export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.322.b06-1.el7_9.x86_64
export PATH=.:$JAVA_HOME:$JAVA_HOME/bin:$PATH

export HADOOP_HOME=/opt/hadoop/hadoop
export PATH=.:$JAVA_HOME:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH

source /root/.bash_profile # 生效
```

```bash
# 回到node01主机器
source /root/.bash_profile

cd /opt/hadoop/hadoop/bin
hdfs namenode -format

cd /opt/hadoop/hadoop/sbin
start-dfs.sh
start-yarn.sh

```

此时在主节点node01 输入 jps可以看到已经成功启动的进程：

![截屏2022-02-26 18.19.47](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-26%2018.19.47.jpg)

此时在从属节点node012输入 jps可以看到已经成功启动的进程：![截屏2022-02-26 18.19.15](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-26%2018.19.15.jpg)

-----------------------------------------------------------------------------------------------------------

特殊情况补充：

​		如果 jps 之后 看见了 

​		【进程号】 – process information unavailable

​		这时可以通过进入本地文件系统的/tmp目录下，删除名称为
hsperfdata_{username}的文件夹，然后重新启动Hadoop。
注意：要将所有用户的这个类似的文件夹全部删除掉才行。

```bash
cd /tmp 
ls | grep hsperfdata
# 依次删除即可。
```

![截屏2022-03-01 20.51.31](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-03-01%2020.51.31.jpg)

****



```bash
# 在node01的浏览器中输入 
node01:9870
```

![截屏2022-02-26 23.05.27](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-26%2023.05.27.jpg)

![截屏2022-02-26 23.07.40](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-26%2023.07.40.jpg)

```bash
# 在node01的浏览器中输入 
node01:8088
```

![截屏2022-02-26 23.08.36](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-26%2023.08.36.jpg)

​		可以看见我只有两个运行的结点，子结点只有一个，实际上应该至少搞三个子结点，重复操作几遍新建结点的步骤即可。



# 安装配置完之后的小插曲：

## 第二天		

​		第二天我重新又装了一遍Hadoop练手，jps后发现secondarynamenode死活没有启动，按照网上的建议删进程重新开服务也不行，我只好去logs里找：

![截屏2022-02-27 19.26.09](centos7%E5%AE%89%E8%A3%85Hadoop.assets/%E6%88%AA%E5%B1%8F2022-02-27%2019.26.09.jpg)

​		发现问题原来出在这里！！

```bash
vim /opt/hadoop/hadoop/etc/hadoop/hdfs-site.xml
```

```bash
<property>
        # <name>dfs.namenode.secondary.http-address</name>
        # 这里好危险，我找logs日志才发现这里写错了。
        <name>dfs.namenode.http-address</name>
        <value>node01:9870</value>
</property>
```

​		结论：一定要养成常看logs文件排错的好习惯！



## 半个月以后

​		问题：在slave子机器node02 上 启动Hadoop时，通过jps目录发现没有datanode进程。

​		解决方案: clusterID不匹配导致的问题，步骤如下：

```bash
1、执行./stop-all.sh关闭集群
2、在slave机器上直接删除Hadoop。
3、在master上 删除存放hdfs数据块的文件夹(hadoop/tmp/)，然后重建该文件夹
4、在master上 删除hadoop下的日志文件logs，我的在hadoop/data/logs，这个直接删除即可。
5、在master上 把 namenode和datanode 存放位置的文件夹删除，我的在 hadoop/data/name和hadoop/data/data。
6、在master上 将Hadoop文件发送到slave机器：scp -r /opt/hadoop/hadoop  node02:/opt/hadoop
7、在master上 执行hadoop namenode -format格式化hadoop
# 重启hadoop集群

# 网上的说法都是由于进行hadoop格式化的时候没有事先结束所有进程，或者多次进行了format导致的datanode的clusterID 和 namenode的clusterID不匹配，从而在启动后没有datanode进程。我的原因是hive执行命令时不小心overwrite了关键文件夹，以后重写一定要小心！

# 如果装了hive ，完成上述步骤之后，在master机器内如下操作。
1、再mysql内删除 hive生成的存储元数据的database（从hive-site.xml 中可以看见 我之前设置的就是 一个叫 ”hive“ 的databases，里面74个表）。
2、开启Hadoop服务。
3、在hive 的bin中初始化metadata，指定元数据在mysql当中。
		cd /opt/hive/apache-hive-3.1.2-bin/bin
		schematool -initSchema -dbType mysql -verbos
```

