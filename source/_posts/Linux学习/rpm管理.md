---
layout: posts
title: "linux_rpm管理"
date: 2022-10-29 15:42:07
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Linux"
tags:
- "linux"
---

简介 <!--more-->

		软件包管理工具

​		redhat package manager-rpm

​		suse\redhat\centos 版本都有。

# rpm查询

```bash
rpm -q 软件包
rpm -qa | grep firefox  # 查有没有安装火狐软件
```

![image-20220115205028658](rpm%E7%AE%A1%E7%90%86.assets/image-20220115205028658.png)

# rpm卸载	

```bash
rpm -e 软件包  # 不用写软件包全名
# 可能提示卸载后会破坏某个程序的运行。
rpm -e --nodeps 软件包 # 强制删除软件包。不建议

```

# rpm安装

```bash
rpm -ivh 已经下载的软件包位置及全名 # install verbose提示 hash进度条。

```

