---
layout: posts
title: "ubuntu拓展目录大小"
date: 2024-10-8 12:12:07
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Linux"
tags:
- "扩容"
- "ubuntu"
---

简介 <!--more-->

# 前提：

硬盘500g，只显示100g可用。



# 处理：

```sh
sudo vgdisplay #查看卷组和逻辑卷的情况

# 假设您的逻辑卷名称是 ubuntu--vg-ubuntu--lv（从之前的 lsblk 输出中推）
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv

# 还需要扩展文件系统以使用新的空间。假设根目录使用的是 ext4 文件系统（从之前的 lsblk 输出可以看到）
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv

# 确认文件系统是否已经成功扩展
df -h /

```

补充

```sh
# 如果显示类型为 xfs，那么您可以使用以下命令扩展文件系统
sudo xfs_growfs /

# 使用的是 Btrfs 文件系统，您可以使用 btrfs 工具进行扩展
sudo btrfs filesystem resize max /

```



# 总结：

ext4 文件系统：使用 resize2fs 扩展。
XFS 文件系统：使用 xfs_growfs 扩展。
Btrfs 文件系统：使用 btrfs filesystem resize 扩展。

