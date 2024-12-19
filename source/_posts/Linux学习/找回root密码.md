---
layout: posts
title: "linux找回root密码"
date: 2022-10-29 15:42:07
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Linux"
tags:
- "linux"
---

简介 <!--more-->

​		1、开机按    e    进入编辑界面。

​		2、在” Linux16 “开头，“utf-8” 结尾的内容后输入： init=/bin/sh  

​		3、按快捷键 ctrl + x 进入单用户模式。

​		4、在光标位置 输入： mount -o remount,rw / ( 注意各个单词间有空格)

​		5、在新的一行输入： passwd

​		6、输入新密码；回车；输入第二遍新密码。

​		7、输入 ：  touch  /.autorelabel （注意空格）

​		8、输入：exec  /sbin/init       等待系统自动修改密码，时间会有点长。

​		