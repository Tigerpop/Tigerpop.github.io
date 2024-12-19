---
layout: posts
title: windows_ubuntu双系统安装
date: 2022-10-29 23:16:30
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "windows_ubuntu双系统"
tags:
- "ubuntu"
---

简介 <!--more-->


```shell
 # 安装Ubuntu windows双系统

准备两个u盘  A B。

1 用 windows 上的 rufus（知乎搜） 软件制作 系统启动盘 A 和 B ，
 先检查主板是否UEFI启动。关闭fast boot。

2.ISO文件 Ubuntu的去官网下载，Windows的去  http://msdn.itellyou.cn/  用迅雷下载

3.安装windows

4.给Windows分出一个 60 G 的未分配磁盘。顺便检查主板是UEFI启动的吗？

5.装Ubuntu，如果没有核显，用nvidia的显卡，启动然后 按住shift 不动，再按 e，
  将  quite splash 改为 quite splash nomodeset
  f10 安装

6.在安装中选自定义安装模式，从未分配的磁盘中按"+"分出300m的boot、16g交换分区、 主分区等若干个分区，
  尤其注意要在下方的挂载选项选择 刚刚设置好的boot分区编号。安装。

7.安装结束后，启动系统出现黑屏

  开机时按住shift不放，出现grub画面，按'''e'''
  进入编辑开机指令的模式（或者开机直接按“e”）, 同样找到'''quitesplash'''
  并在后面加上“nomodeset”，按''F10''启动系统

8.装好Ubuntu后
    sudo vi /etc/default/grub
    按‘ℹ’进入vim的编辑模式
    GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
    #修改为：
    GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nomodeset"

9.在Ubuntu中手动更新nvidia 驱动。

10. 享受双系统吧。








```

