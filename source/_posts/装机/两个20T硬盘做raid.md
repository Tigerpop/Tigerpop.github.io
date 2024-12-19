---
layout: posts
title: 两个20T硬盘做raid
date: 2024-10-21 23:12:18
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "装机"
tags:
- "raid"
- "挂载"
---



# 先说结论

不推荐这样在系统层面作raid，因为性能发挥不出来。以下所有都是自娱自乐，干活还得是在bios层面先做好raid，系统直接识别成一个盘，性能才能发挥出来。



# 准备工作

## 识别

拿到硬盘以后，首先还是要识别一下硬盘。

```sh
lsblk

lsblk -f

sudo blkid
```

一般可以看见多了一个 sda 或者sdb 的磁盘，我的两块磁盘都是20T的比较大。

## 创建分区

依次对两个磁盘创建分区

```sh
sudo apt install gdisk # 这里不用fdisk了，创建新的 GPT 分区表 所以用gdisk（2T分界线）。

sudo gdisk /dev/sda # 和fdisk类似，跟着提示来就行，n ... w 
```

## 格式化分区

依次对两个磁盘的新分区格式化

```sh
# 16T单个文件分界线，大盘推荐 xfs 格式。
sudo apt update
sudo apt install xfsprogs
sudo mkfs.xfs /dev/sda1 # 你的磁盘下的分区如sda1 sda2 或者 sdb1、sdc1等等。
```

此时 `lsblk -f` 可见 sda磁盘的下面多了一个 sda1的分区，而且格式为 xfs；sdb磁盘的下面多了一个 sdb1的分区，而且格式为 xfs。

![1729002987963](%E4%B8%A4%E4%B8%AA20T%E7%A1%AC%E7%9B%98%E5%81%9Araid/1729002987963.jpg)





# raid

## 制作raid设备

在你创建 RAID  阵列时，新的 RAID 阵列会覆盖原有的数据，这样所有多个磁盘上的数据都会被清理干净。

### （一）选择制作做raid1

20T 和20T作 raid1是一个备份，可以防止数据丢失，但是空间只用的上20T了。

```sh
sudo apt update
sudo apt install mdadm

#  /dev/md0 是 RAID 设备的名称。
#  --level=1 指定 RAID 级别为 1。
#  --raid-devices=2 指定 RAID 中的设备数量。
sudo mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sda1 /dev/sdb1
```

### （二）选择制作做raid0

20T 和20T作 raid0是纯粹的扩大空间，空间40T了。

```sh
sudo apt update
sudo apt install mdadm

#  /dev/md0 是 RAID 设备的名称。
#  --level=0 指定 RAID 级别为 0。
#  --raid-devices=2 指定 RAID 中的设备数量。
sudo mdadm --create --verbose /dev/md0 --level=0 --raid-devices=2 /dev/sda1 /dev/sdb1
```

### （三）选择制作做raid5

```sh
sudo apt update
sudo apt install mdadm

#  /dev/md0 是 RAID 设备的名称。
#  --level=0 指定 RAID 级别为 5。
#  --raid-devices=4 指定 RAID 中的设备数量。
sudo mdadm --create /dev/md0 --level=5 --raid-devices=4 /dev/sda1 /dev/sdb1 /dev/sdc1 /dev/sdd1
```



## 格式化raid设备

```sh
sudo mkfs.xfs /dev/md0
```

## 创建挂载点

```sh
sudo mkdir -p /data_raid
```

## 1、选择临时挂载

```sh
sudo mount /dev/md0 /data_raid

# 如果出现 
# mount: /data_raid: can't read superblock on /dev/md0.
sudo mdadm --detail /dev/md0 # 看state 状态：是不是在 recovering
cat /proc/mdstat # 看恢复进度
# 等恢复进度 100% ，再去挂载。恢复可能会很久。
```

## 2、选择永久性挂载

```sh
sudo blkid /dev/md0 # 获取raid设备的uuid。

sudo vim /etc/fstab
# 加入 如下行
UUID=98fae9c1-4ea5-4b4c-9b98-d81d4d5e3c44 /data_raid xfs defaults,nofail 0 0
#  raid设备的uuid 
#  挂载点
#  xfs  为raid设备的格式
#  defaults：这是挂载选项的组合，表示使用系统的默认设置，包括读写权限、自动挂载等。
#  nofail：允许在启动时，如果这个挂载点无法挂载（例如设备不存在），系统仍然能够正常启动。没有这个选项，系统可能会在挂载失败时停止启动。
#  第一个 0 表示该文件系统不应该被 dump 工具备份。
#  第二个 0 指定该文件系统在启动时的检查顺序。0 表示不进行检查。值为 1 表示在启动时优先检查该文件系统，值为 2 表示依次检查。

# 重新挂载所有文件系统
sudo mount -a

# 如果出现 
# mount: /data_raid: can't read superblock on /dev/md0.
sudo mdadm --detail /dev/md0 # 看state 状态：是不是在 recovering
cat /proc/mdstat # 看恢复进度
# 等恢复进度 100% ，再去挂载。恢复可能会很久。
```

## 检查 RAID 阵列的状态

```sh
cat /proc/mdstat # 如果创建成功，会看到 /dev/md0（raid设备） 的信息
```

![daa382604b1efef131d4b878318c935](%E4%B8%A4%E4%B8%AA20T%E7%A1%AC%E7%9B%98%E5%81%9Araid/daa382604b1efef131d4b878318c935.png)

```sh
lsblk -f
```

![1729002280504](%E4%B8%A4%E4%B8%AA20T%E7%A1%AC%E7%9B%98%E5%81%9Araid/1729002280504.jpg)

![1729004340071](%E4%B8%A4%E4%B8%AA20T%E7%A1%AC%E7%9B%98%E5%81%9Araid/1729004340071.jpg)

```sh
sudo blkid /dev/md0 # 查看挂载情况
```

![1729002433599](%E4%B8%A4%E4%B8%AA20T%E7%A1%AC%E7%9B%98%E5%81%9Araid/1729002433599.jpg)

```sh
df -h 
```

![1729002547062](%E4%B8%A4%E4%B8%AA20T%E7%A1%AC%E7%9B%98%E5%81%9Araid/1729002547062.jpg)

![1729004403164](%E4%B8%A4%E4%B8%AA20T%E7%A1%AC%E7%9B%98%E5%81%9Araid/1729004403164.jpg)





# 删除raid设备

```sh
# 看看 有没有md0
lsblk

# 检查当前RAID阵列
sudo mdadm --detail /dev/md0 

# 停止现有的RAID阵列（这一步会停止现有的 RAID 阵列。如果阵列正在运行，数据访问会中断。）
sudo mdadm --stop /dev/md0

#  删除现有的RAID阵列（这一步会将 RAID 阵列从系统中移除，这意味着系统不再识别该 RAID 阵列。）
sudo mdadm --remove /dev/md0

# 删除设备上的RAID元数据
# （由于 RAID 阵列的破坏，阵列上的数据将不可访问。即使物理数据可能仍然存在于磁盘分区中，但由于没有 RAID 的支持，无法直接访问和恢复这些数据。）
sudo mdadm --zero-superblock /dev/sda1
sudo mdadm --zero-superblock /dev/sdb1
sudo mdadm --zero-superblock /dev/sdc1
sudo mdadm --zero-superblock /dev/sdd1

```





# 期间可能遇到的问题

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

