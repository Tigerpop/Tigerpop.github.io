---
layout: posts
title: "conda虚拟环境中用pip安装注意事项"
date: 2021-12-15 13:02:10
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "anaconda"
tags:
- "conda"
- "pip"
---

   进入conda 虚拟环境 ，pip最好指定安装位置，到相应虚拟环境的site-package，以防安装位置错乱。

具体操作流程：

1. 先进入conda 的虚拟环境，用pip -V 查看 相应虚拟环境里的pip 对应的site-package 位置。
   例如： /Users/chenyushao/anaconda3/envs/xgb/lib/python3.7/site-packages/pip (python 3.7)
2. （以例子说明）在对应的虚拟环境中 
   sudo pip install 【包名】 --target=/Users/chenyushao/anaconda3/envs/xgb/lib/python3.7/site-packages

（还可以在此基础上补充用清华或者中科院的源提高下载速度）
sudo pip install 【包名】 --target=/Users/chenyushao/anaconda3/envs/xgb/lib/python3.7/site-packages -i https://pypi.mirrors.ustc.edu.cn/simple



按照 1-2 步骤操作后，就仅在当前虚拟环境中安装了此包，与base的环境、其他环境没有关系。尤其注意：进入虚拟环境后直接pip安装，有可能会安装到base环境的pip下载汇总中，也有可能下载到基础环境的pip下载汇总中。