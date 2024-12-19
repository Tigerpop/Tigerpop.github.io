---
layout: posts
title: 服务器集群添加GPU
date: 2024-10-20 20:32:28
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "装机"
tags:
- "GPU"

---



# 背景

我加了一台服务器到服务器集群中，服务器中有两张GPU卡，我需要将这两张卡添入服务器下的两个虚拟机中使用。



# 理论

**vGPU**： 虚拟化了一堆Nvidia的卡给一群机器用，就需要英伟达要授权，死贵的。

**GPU** ：仅仅能给自己机器上用，比如服务器下有两个虚拟机，两张显卡，这两张显卡就只能被这两个虚拟机用，服务器之外的别的服务器是用不了这两GPU的。

**iommu** ：I0MMU是一个内存管理单元Q机制，它可以实现把设备访问的虚拟地址(VA)转化成物理地址(PA);说人话就是开了它虚拟机才能访问到物理机的硬件，如显卡。



# 网页版实操

如有看见了ECC开启了要先关闭ECC，没有就跳过。

![要在添加完硬件再启动ECC](%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%9B%86%E7%BE%A4%E6%B7%BB%E5%8A%A0GPU/%E8%A6%81%E5%9C%A8%E6%B7%BB%E5%8A%A0%E5%AE%8C%E7%A1%AC%E4%BB%B6%E5%86%8D%E5%90%AF%E5%8A%A8ECC.jpg)

在物理机设置打开 iommu，让虚拟机可以访问到GPU。

![高级设置](%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%9B%86%E7%BE%A4%E6%B7%BB%E5%8A%A0GPU/%E9%AB%98%E7%BA%A7%E8%AE%BE%E7%BD%AE.jpg) 

保存、重启。

![加GPU硬件](%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%9B%86%E7%BE%A4%E6%B7%BB%E5%8A%A0GPU/%E5%8A%A0GPU%E7%A1%AC%E4%BB%B6.jpg)

![加2](%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%9B%86%E7%BE%A4%E6%B7%BB%E5%8A%A0GPU/%E5%8A%A02.jpg)

![加3](%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%9B%86%E7%BE%A4%E6%B7%BB%E5%8A%A0GPU/%E5%8A%A03.jpg)

![加4](%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%9B%86%E7%BE%A4%E6%B7%BB%E5%8A%A0GPU/%E5%8A%A04.jpg)

![加5](%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%9B%86%E7%BE%A4%E6%B7%BB%E5%8A%A0GPU/%E5%8A%A05.jpg)

![加6](%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%9B%86%E7%BE%A4%E6%B7%BB%E5%8A%A0GPU/%E5%8A%A06.jpg)

![加7](%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%9B%86%E7%BE%A4%E6%B7%BB%E5%8A%A0GPU/%E5%8A%A07.jpg)

检查

![检查](%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%9B%86%E7%BE%A4%E6%B7%BB%E5%8A%A0GPU/%E6%A3%80%E6%9F%A5.jpg)