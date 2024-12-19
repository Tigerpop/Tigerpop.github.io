---
layout: posts
title: Ubuntu装clash
date: 2024-12-16 21:09:57
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "windows_ubuntu双系统"
tags:
- "ubuntu"
- "clash"
---

简介 <!--more-->



# 下载

Github 上的 clash 官方仓库已经删库跑路，本文介绍的是一个目前可行的解决方案。

在 https://github.com/doreamon-design/clash/releases 下载与自己系统架构符合的安装包。一般选择 `[clash_2.0.24_linux_amd64.tar.gz](https://github.com/doreamon-design/clash/releases/download/v2.0.24/clash_2.0.24_linux_amd64.tar.gz)`。



# 安装

```bash
gzip -d clash_2.0.24_linux_amd64.tar.gz # 在安装包所在路径解压安装包
chmod +x clash_2.0.24_linux_amd64.tar.gz # 给解压后的文件赋予执行权限
mv clash_2.0.24_linux_amd64 /usr/local/bin/clash # 移动文件位置


# 一、 把clash_2.0.24_linux_amd64文件夹中 clash执行文件和 license 移动到 /usr/local/bin/clash
# 二、 在bashrc 或者 zshrc 中 把 /usr/local/bin/clash 加入到环境变量路径。
# 三、如果缺少MMDB文件：
#    下载 Country.mmdb 文件： 在另一台可以访问网络的设备上，手动下载 Country.mmdb 文件：
#    URL: https://cdn.jsdelivr.net/gh/Dreamacro/maxmind-geoip@release/Country.mmdb
#    将文件复制到 Clash 的配置目录：  ~/.config/clash/

clash -v # 查看是否成功安装
```



# 配置

```bash
clash # 启动 Clash，生成 ~/.config/clash/config.yaml 文件
cd ~/.config/clash/ # 进入目录配置文件
wget -O config.yaml 订阅地址 # 把订阅地址的配置文件导入本地配置文件
echo -e "export http_proxy=http://127.0.0.1:7890\nexport https_proxy=http://127.0.0.1:7890" >> ~/.bashrc # 配置环境变量
```



# 修改系统代理

![截屏2024-12-16 19.04.22](Ubuntu%E8%A3%85clash/%E6%88%AA%E5%B1%8F2024-12-16%2019.04.22.jpg)



# 进入界面

启动后在打开  http://clash.razord.top/#/settings  进入节点管理的 UI 页面。

![微信图片_20241216191308](Ubuntu%E8%A3%85clash/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20241216191308.jpg)



# 可以科学上网了

![微信图片_20241216191357](Ubuntu%E8%A3%85clash/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20241216191357.jpg)





参考：https://eaglebear2002.github.io/%E6%8A%80%E6%9C%AF%E7%A7%91%E6%99%AE/Ubuntu%2022.04%20%E5%AE%89%E8%A3%85%20Clash/