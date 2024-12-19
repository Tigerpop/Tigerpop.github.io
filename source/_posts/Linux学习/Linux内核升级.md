---
layout: posts
title: "linux内核升级"
date: 2022-10-29 15:42:07
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Linux"
tags:
- "linux"
---

简介 <!--more-->

```bash
uname -a # 查看内核版本
yum info kernel -q #显示可以升级的匹配内核
yum update kernel # 升级内核
yum list kernel -q # 查看有几个内核

reboot # 重启后，选择一个内核版本启动
# 原来的文件都在，不受影响。

```

