---
layout: posts
title: bios内作raid
date: 2024-10-21 20:52:20
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "装机"
tags:
- "raid"
---



# 一、bios内设置raid

1.开机连续点击 F10 进入 BIOS，选择【UEFI Drivers】。如果硬盘连接的是 SATA

接口，选择【intel(R)RSTe SATA Controller】选项。如果连接的是 Ssata 接口选择

【inter(R)RSTe Ssata Controller】选项

![1](bios%E5%86%85%E4%BD%9Craid/1.jpg)

2.进入后选择【Create RAID Volume】，可以创建新的 RAID。

![2](bios%E5%86%85%E4%BD%9Craid/2.jpg)

3.【RAID Level】处选择需要设置的 RAID 级别

![3](bios%E5%86%85%E4%BD%9Craid/3.jpg)

4.在硬盘信息下方，可选择想要创建新 RAID 的硬盘。

![4](bios%E5%86%85%E4%BD%9Craid/4.jpg)

5.如果需要使用哪块硬盘，就将哪块硬盘下方选框中改成【X】。

![5](bios%E5%86%85%E4%BD%9Craid/5.jpg)

6.点击硬盘信息后面的【问号】，可以查看对应硬盘的详细信息

![6](bios%E5%86%85%E4%BD%9Craid/6.jpg)

7.选择完需要组成 RAID 的硬盘后，点击下方【Create Volume】创建 RAID

![7](bios%E5%86%85%E4%BD%9Craid/7.jpg)

8.创建完成后会自动返回到上一级界面，在创建 RAID 下面会多出来一行刚刚创建的 RAID 信息

![8](bios%E5%86%85%E4%BD%9Craid/8.jpg)



# 二、磁盘格式化、挂载

```sh
lsblk -f # 查看
```

![pp](bios%E5%86%85%E4%BD%9Craid/pp.jpg)

```sh
sudo mkfs.xfs /dev/mdXXXXX # 格式化

sudo mkdir -p XXXXX # 建挂载点文件夹

# 临时挂载
sudo mount /mdXXXXX /XXXXX
# 永久挂载
sudo vim /etc/fstab 
/dev/mdXXXXX /data_raid xfs defaults,nofail 0 0 # 写入这个
```

![pp2](bios%E5%86%85%E4%BD%9Craid/pp2.png)



# 三、期间可能遇到的问题

遇到 `  wrong fs type, bad option, bad superblock on /dev/vdc1` 等等类似的 xfs 挂载失败 的问题，

## 解决方法

**参考**：

中间出处：	https://armstrong.viyf.org/articles/2023/08/mount-wrong-fs-type-bad-option-bad-superblock.shtml

源出处：	https://serverfault.com/questions/948408/mount-wrong-fs-type-bad-option-bad-superblock-on-dev-xvdf1-missing-codepage

**原因**：XFS 为了防止同一个文件系统被多次挂载，在格式化硬盘并创建文件系统时会给文件系统打上 UUID。挂载分区时，XFS 检测到当前挂载了相同 UUID 的分区，就会拒绝执行挂载。

**结论**，解决方法就是 不用uuid挂载。

```sh
# 临时挂载
sudo mount -o defaults,nouuid /dev/vdc1 /data

# 永久挂载
# 在 /etc/fstab 中写入
/dev/vdc1   /data   xfs   defaults,nouuid   0   0

```

