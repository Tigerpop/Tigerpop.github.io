---
layout: posts
title: "linux目录文件"
date: 2022-10-29 15:42:07
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Linux"
tags:
- "linux"
---

简介 <!--more-->

​		Linux下一切皆为文件！

​		Linux会把硬件都映射为一个文件来进行统一管理，比如cpu什么的。

# /bin

​		binary缩写，存放最近常用的命令，例如cd命令。

# /sbin

​		s 是 super user，指的是系统管理员，bin一样。

# /home

​		普通用户主目录。主目录下 useradd jack命令就创建了另一个用户。

# /root

​		系统管理员。

# /lib

​		动态链接共享库。

# /lost + found

​		一般是空，里面存放非法关机数据。

# /etc

​		所有系统管理所需要配置的文件和子目录，比如mysql 就是my.conf 。

# /usr

​		用户的应用程序和文件，类似Windows的program files。

# /boot

​		引导部分。

# /proc /srv /sys

​		系统内存映射，改动很容易就系统崩溃。

# /tmp

​		临时文件。

# /dev

​		设备管理器，把所有的硬件用文件的形式管理。

# /media

​		识别的设备，u盘、光驱什么的，挂载到这里。

# /mnt

​		系统在该目录让用户临时挂载别的文件系统，我们可以将外部存储挂载在/mnt/上，然后进入该目录就能看里面的内容了。

# /opt

​		理论上待安装文件要放在这里。

# /ust/local

​		另一个安装软件的目录，一般是通过编译源码形式安装程序。

# /var

​		习惯于存放log日志文件。常被修改的文件。

