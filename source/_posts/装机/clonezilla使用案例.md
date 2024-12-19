---
layout: posts
title: clonezilla使用案例
date: 2024-10-15 11:32:10
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "装机"
tags:
- "ventoy"
- "clonezilla"

---



clonezilla 是个好东西，可以克隆硬盘、制作镜像等等。



# 克隆硬盘步骤（简单版）

1、我用一个 64g的u盘制作了一个 ventoy 工具盘，空余空间为12g，clonezilla的ISO文件是放在 这个u盘中的。开机选择 这个u盘启动，注意格式。

![111](clonezilla%E4%BD%BF%E7%94%A8%E6%A1%88%E4%BE%8B/111.jpg)



2、选择 语言



3、选择使用“重生龙”



4、选择 “从硬盘/分区克隆到硬盘/分区”



5、初级模式



6、是否保留原分区形式，选是。



7、y...y...y...y y 一路确认。



8、选择 克隆完成以后 reboot 还是 poweroff，我一般选了reboot。



9、我一个nvme 的512g的硬盘 克隆到 另一个nvme的512g硬盘中，ventoy的U盘64g，12g空余，一共耗时4小时。



# 一个失败案例

![aaaebb506391433eaca83d403765733](clonezilla%E4%BD%BF%E7%94%A8%E6%A1%88%E4%BE%8B/aaaebb506391433eaca83d403765733.jpg)

如果出现 上面这样的显示，说明不仅仅是 分辨率的问题，很有可能是对u盘中的 clonezilla 读取出现啦一些问题。我在遇到这样的显示之后，克隆的过程中遇到了 下面的报错。

## 报错

克隆的过程中 速度非常非常慢。预计要30小时，我就发现了又问题，然后在克隆5%时候报出如下错误：

```sh
报错 /usr/share/brb1/sbin/ocs-functions: line 15141: 41591 killed.  partclone.dd -z 10485760 -N -L /var/log/partclone.log -s /dev/sdc3 -0 /dev/sdd3  Failed to clone /dev/sdc3 to /dev/sdd3 press "Enter" to continue... 
```

## 解决

这样的报错 可能是 U盘读写性能出现了问题，也可能是 两块硬盘中出现了坏块等等问题，我很难锁定具体问题。

于是我换了一台电脑，在这台电脑上插上 ventoy的U盘和 源、目的两个硬盘，重新操作了一遍，这次没有报错。

![fd0cc1352fd4e95595041bf384d48ce](clonezilla%E4%BD%BF%E7%94%A8%E6%A1%88%E4%BE%8B/fd0cc1352fd4e95595041bf384d48ce.jpg)

预计4小时完成，2G/min 克隆速度 属于正常的水平。于是成功克隆。