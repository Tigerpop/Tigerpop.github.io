---
layout: posts
title: Ventoy工具
date: 2024-10-13 11:32:10
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "装机"
tags:
- "ventoy"
- "clonezilla"
---



# 背景

​		我之前用的rufu 工具一个u盘只能制作一个 系统启动盘，我买了一堆u盘，挺搞笑的。

​		其实用这个 ventoy 工具 制作u盘就可以让一个u盘制作成多系统安装工具，一个u盘就可以进入多个不同的系统，像什么 clonezilla Windows7 Windows10 Windows11 ubuntu centos 等等啦。



# 下载使用

## 下载

​	下载网址：      (https://www.ventoy.net/cn/)

![截屏2024-10-13 21.37.30](ventoy%E5%B7%A5%E5%85%B7/%E6%88%AA%E5%B1%8F2024-10-13%2021.37.30.jpg)



## 使用

![1728826068849](ventoy%E5%B7%A5%E5%85%B7/1728826068849.jpg)

右边 `设备内部Ventoy` 指的是 你准备做成 系统启动盘的u盘。



![2b3b9696779f971f80a659446915fb0](ventoy%E5%B7%A5%E5%85%B7/2b3b9696779f971f80a659446915fb0.jpg)

有的老机器 是 用 MBR，但是这里推荐都用 GPT把，在 启动设备的 bios 中选择 UEFI标注的的那个U盘启动，这个挺重要的。



![6db4e84b7ae6384f692166a62ba773a](ventoy%E5%B7%A5%E5%85%B7/6db4e84b7ae6384f692166a62ba773a.png)

推荐把U盘做成 exfat格式，这样通用性好很多，方便除了作系统以外 存储文件。



然后u盘制作好啦，仅仅需要把ISO文件放进u盘就好啦。

以下是 通过 ventoy启动的u盘例子

![111](ventoy%E5%B7%A5%E5%85%B7/111.jpg)





# 典型案例

出现的问题  `“Windows 无法安装到这个磁盘，选中的 磁盘采用 GPT 分区形式 ”`

磁盘是 GPT 分区形式，就要用UEFI来启动u盘，此时开机的bios 要打开 UEFI选项，然后bios 的boot 中也可能有两个 u盘选项，我们记得选 EFI 或者 UEFI前缀的那个u盘。



# 补充 ：clonezilla

推荐一下 一个有用的工具  ，可以克隆磁盘a到磁盘b、 恢复磁盘巴拉巴拉等等 很多事情。

**Clonezilla**

(https://clonezilla.org/downloads.php)

![baad0f58040d1cbfca98fa20a827a1f](ventoy%E5%B7%A5%E5%85%B7/baad0f58040d1cbfca98fa20a827a1f.png)

这里推荐 基于ubuntu或者 debian 开发的 stable稳定版。



(https://clonezilla.org/downloads/download.php?branch=alternative)

![af3c243f051f31c65e236318e470135](ventoy%E5%B7%A5%E5%85%B7/af3c243f051f31c65e236318e470135.png)

这里记得选 ISO格式的文件，这样它就能被扔进 ventoy 制作的u盘中使用啦。