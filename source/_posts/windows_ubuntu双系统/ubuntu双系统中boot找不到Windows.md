---
layout: posts
title: ubuntu双系统中boot找不到Windows
date: 2024-10-28 23:15:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "windows_ubuntu双系统"
tags:
- "boot"

---

简介 <!--more-->



# 问题

**在 Ubuntu、Windows双系统，GNU GRUB 中看不见Windows的boot选项。**



# 解决步骤

进入ubuntu系统，Ubuntu 和别的系统组成了多系统，最好要以Ubuntu为主。

1、编辑 /etc/default/grub 

```sh
GRUB DEFAULT=0
GRUB TIMEOUT STYLE=menu      # 改成 menu
GRUB TIMEOUT=20              # 时间改久一点
GRUB DISTRIBUTOR='lsb release -i -s 2> /dev/null I I echo Debian'
GRUB_ CMDLINE LINUX DEFAULT="quiet splash system.unified croup hierarchy=0"
GRUB CMDLINE LINUX=""
GRUB DISABLE OS PROBER=false # 没有就添加
```

2、sudo update-grub  更新引导菜单

```sh
sudo update-grub  # 运行完可见 found Windows boot manager on XXXXX ... done
```

3、重新启动可见GNU GRUB 中多了一个Windows选项。