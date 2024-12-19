---
layout: posts
title: spark_maven学习
date: 2022-10-29 22:39:44
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "spark"
tags:
- "maven"
---

简介 <!--more-->

		Maven 翻译为"专家"、"内行"，是 Apache 下的一个纯 Java 开发的开源项目。基于项目对象模型（缩写：POM）概念，Maven利用一个中央信息片断能管理一个项目的构建、报告和文档等步骤。

​		Maven 是一个项目管理工具，可以对 Java 项目进行构建、依赖管理。

​		Maven 功能

​		Maven 能够帮助开发者完成以下工作：	

- 构建
- 文档生成
- 报告
- 依赖
- SCMs
- 发布
- 分发
- 邮件列表

# 约定配置

​		Maven 提倡使用一个共同的标准目录结构，Maven 使用约定优于配置的原则，大家尽可能的遵守这样的目录结构。如下所示：

| 目录                               | 目的                                                         |
| :--------------------------------- | :----------------------------------------------------------- |
| ${basedir}                         | 存放pom.xml和所有的子目录                                    |
| ${basedir}/src/main/java           | 项目的java源代码                                             |
| ${basedir}/src/main/resources      | 项目的资源，比如说property文件，springmvc.xml                |
| ${basedir}/src/test/java           | 项目的测试类，比如说Junit代码                                |
| ${basedir}/src/test/resources      | 测试用的资源                                                 |
| ${basedir}/src/main/webapp/WEB-INF | web应用文件目录，web项目的信息，比如存放web.xml、本地图片、jsp视图页面 |
| ${basedir}/target                  | 打包输出目录                                                 |
| ${basedir}/target/classes          | 编译输出目录                                                 |
| ${basedir}/target/test-classes     | 测试编译输出目录                                             |
| Test.java                          | Maven只会自动运行符合该命名规则的测试类                      |
| ~/.m2/repository                   | Maven默认的本地仓库目录位置                                  |

# Maven规范化构建流程

![截屏2022-05-26 11.10.53](maven%E5%AD%A6%E4%B9%A0.assets/%E6%88%AA%E5%B1%8F2022-05-26%2011.10.53.jpg)

# maven 工程的目录结构

![截屏2022-05-26 11.17.37](maven%E5%AD%A6%E4%B9%A0.assets/%E6%88%AA%E5%B1%8F2022-05-26%2011.17.37.jpg)

![截屏2022-05-26 11.17.17](maven%E5%AD%A6%E4%B9%A0.assets/%E6%88%AA%E5%B1%8F2022-05-26%2011.17.17.jpg)

# idea中自带的maven

​		idea中java项目自带maven，也可以新建一个maven项目，然后在maven项目中双击src->main->java ，在maven项目中创建 python等其他语言的模块集。![截屏2022-05-26 16.34.28](maven%E5%AD%A6%E4%B9%A0.assets/%E6%88%AA%E5%B1%8F2022-05-26%2016.34.28-7050421.jpg)

​		例如，在maven项目中我们又额外 创建了一个python模块集，如下：

![截屏2022-05-26 16.36.05](maven%E5%AD%A6%E4%B9%A0.assets/%E6%88%AA%E5%B1%8F2022-05-26%2016.36.05.jpg)

# pyspark中调用包间接使用maven

​		我们要使用一个包，例如(spark-sql-kafka)：

```shell
<!-- https://mvnrepository.com/artifact/org.apache.spark/spark-sql-kafka-0-10 -->
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-sql-kafka-0-10_2.13</artifactId>
    <version>3.2.1</version>
    <scope>test</scope>
</dependency>
```

​		在pyspark中，我们运行如下：

```shell
bin/spark-submit --packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.2.1 /opt/data/sparkstreaming/structured_receive_from_kafuka.py

# --packages 后面跟的是maven格式中的groupId:artifactId:version,中间用 : 隔开。
# 运行一次以后，我们不用去管，等待一阵子，maven会被自动下载好，并且上面的相关依赖jar包也会下载好。
# 之后程序正常运行。
```

​		如果在运行中出现如下错误：

`py4j.protocol.Py4JJavaError: An error occurred while calling o41.save.
: java.lang.NoClassDefFoundError: scala/$less$colon$less`

​		则说明新引入的jar包Scala版本，和spark的Scala版本出现冲突，我们去spark-shell 中去检查

![截屏2022-05-26 16.53.26](maven%E5%AD%A6%E4%B9%A0.assets/%E6%88%AA%E5%B1%8F2022-05-26%2016.53.26.jpg)

> 说明我们的Scala 要是2.12 的版本，我也觉得挺奇怪，我明明下载的是2.13的版本的Scala，spark3.2.1默认用了Scala2.12。

​		此时，如果maven用到的jar包scala版本是2.13。

`--packages org.apache.spark:spark-sql-kafka-0-10_2.13:3.2.1`

​		就会报错：

`py4j.protocol.Py4JJavaError: An error occurred while calling o41.save.
: java.lang.NoClassDefFoundError: scala/$less$colon$less`

