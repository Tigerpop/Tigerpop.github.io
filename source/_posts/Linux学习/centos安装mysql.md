---
layout: posts
title: "linux安装mysql"
date: 2022-10-29 15:42:07
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Linux"
tags:
- "linux"
---

简介 <!--more-->

1. ​	1.新建文件夹/opt/mysql，并cd进去
2. ​	2.运行wget http://dev.mysql.com/get/mysql-5.7.26-1.el7.x86_64.rpm-bundle.tar，下载mysql安装包

PS：centos7.6自带的类mysql数据库是mariadb，会跟mysql冲突，要先删除。



3.运行tar -xvf mysql-5.7.26-1.el7.x86_64.rpm-bundle.tar 

4.运行rpm -qa|grep mari，查询mariadb相关安装包

5.运行rpm -e --nodeps mariadb-libs，卸载

6.然后开始真正安装mysql，依次运行以下几条

```bash
rpm -ivh mysql-community-common-5.7.26-1.el7.x86_64.rpm

rpm -ivh mysql-community-libs-5.7.26-1.el7.x86_64.rpm

rpm -ivh mysql-community-client-5.7.26-1.el7.x86_64.rpm
# 遇到 下面截图的依赖检测失败，则dnf install ncurses-compat-libs 再安装mysql的 client。

rpm -ivh mysql-community-server-5.7.26-1.el7.x86_64.rpm
```

依赖问题的截图：

![image-20220116114322800](centos%E5%AE%89%E8%A3%85mysql.assets/image-20220116114322800.png)

7.运行systemctl start mysqld.service，启动mysql

8.然后开始设置root用户密码

Mysql自动给root用户设置随机密码，运行grep "password" /var/log/mysqld.log可看到当前密码

9.运行mysql -u root -p，用root用户登录，提示输入密码可用上述的，可以成功登陆进入mysql命令行,mysql命令以；结尾。

10.设置root密码，对于个人开发环境，如果要设比较简单的密码（**生产环境服务器要设复杂密码**），可以运行

set global validate_password_policy=0; 提示密码设置策略

（validate_password_policy默认值1，）

11. set password for 'root'@'localhost' =password('你要设置的密码');

12.运行flush privileges;使密码设置生效。