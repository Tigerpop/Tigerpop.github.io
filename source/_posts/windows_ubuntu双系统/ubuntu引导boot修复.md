---
layout: posts
title: ubuntu引导boot修复
date: 2022-10-29 23:15:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "windows_ubuntu双系统"
tags:
- "ubuntu"
---

简介 <!--more-->

```shell
首先 在u盘（Ubuntu非安装模式）用gparted 将老boot盘移动到最左边。

再参考： https://blog.csdn.net/laocaibcc229/article/details/79274412

sudo add-apt-repository ppa:yannubuntu/boot-repair && sudo apt-get update  

sudo apt-get install -y boot-repair && boot-repair  

自动找到boot分区后，
点击 ：Recommended repair

等待自动修复重启即可。
```

