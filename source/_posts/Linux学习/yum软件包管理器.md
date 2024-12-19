---
layout: posts
title: "linux_yum软件包管理器"
date: 2022-10-29 15:42:07
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Linux"
tags:
- "yum"
---

简介 <!--more-->

​		Shell前端软件包管理器

​		能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软体包，无须繁琐地一次次下载、安装。

​		简单点说, rpm 只能安装已经下载到本地机器上的rpm 包. yum能在线下载并安装rpm包,能更新系统,且还能自动处理包与包之间的依赖问题,这个是rpm 工具所不具备的。

​		类似于python的pip，Mac的brew 作用。

```bash
yum list | grep 软件包

yum install firefox 
```

