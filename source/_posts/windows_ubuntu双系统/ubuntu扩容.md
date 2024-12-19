---
layout: posts
title: ubuntu扩容
date: 2022-10-29 23:09:57
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "windows_ubuntu双系统"
tags:
- "ubuntu"
---

简介 <!--more-->

```shell
######################################################################

参考： https://blog.csdn.net/econe_wei/article/details/101544874

1.在Ubuntu内应用商店下载 Gparted 
 
2.用Ubuntu系统对应的 Ubuntu系统启动 U盘，启用Ubuntu。
	
	2.1 没有核显可能还会遇到启动黑屏的问题，这个可以参考陈宇韶Windows/Ubuntu双系统安装黑屏解决办法。
	    在bios 里U盘启动，别安装，点 Ubuntu（safe graphics），然后别安装，点try ubuntu。

3.在U盘启动的Ubuntu系统里找到刚刚安装好的Gparted。
	3.1 移动未分配区域的位置，让待扩展区域和 未分配区域相连。
	3.2 点击待扩展区域，两头选0，区域扩展。
	3.3 点左上角的勾勾，开始全面设定的操作。

4.步骤3结束后，重启Ubuntu。

######################################################################
```

