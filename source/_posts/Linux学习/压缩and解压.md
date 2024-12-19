---
layout: posts
title: "linux压缩and解压"
date: 2022-10-29 15:42:07
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Linux"
tags:
- "压缩"
---

简介 <!--more-->

# gzip/gunzip

​		针对文件

​		压缩：   gzip 文件

​		解压：   gunzip 文件

# zip/unzip

​		针对文件或者文件夹

```sql
压缩： zip 选项 XXX.zip 压缩内容 
```

​	   (常用选项 -r ，递归压缩即压缩目录。)

```sql
解压： unzip 选项 存放位置 XXX.zip   
```

​	  (常用选项 -d 指定解压后存放的目录。)

# tar	

​		压缩多个文件或者文件夹

```sql
tar 选项 XXX.tar.gz A B C D //打包多个内容为XXX.tar.gz，多个文件用空格隔开
```

```sql
tar -zcvf pc.tar.gz /home/chen/hello.txt /home/chen/mycal.txt /home/chen/mydata.txt
```

​		按照上面那样的写法的话，解压后会有一个home文件夹内嵌chen文件夹再嵌入hello.txt mycal.txt my data.txt 三个文件；而不是直接有hello.txt mycal.txt my data.txt 三个文件。

​		解压 tar.gz文件 到特定文件夹

```sql
tar 选项 XXX.tar.gz -C /home/chen   //解压到chen这个文件夹
```

```sql
tar -zxvf pc.tar.gz -C /home/chen
```

