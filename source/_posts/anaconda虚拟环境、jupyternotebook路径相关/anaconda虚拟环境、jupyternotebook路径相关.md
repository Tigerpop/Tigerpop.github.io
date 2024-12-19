---
layout: posts
title: "anaconda虚拟环境、jupyternotebook路径相关"
date: 2022-10-28 21:07:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "anaconda"
tags:
- "conda"
- "jypyter、ipython"
---

import sys
print(sys.executable) <!--more-->

你需要观察，该命令运行后输出的路径是不是你想运行的那个虚拟环境目录下的python文件。若不是，则你使用jupyter打开的kernel其实就不是你想要的那个虚拟环境。所以总而言之，这是个“如何在anaconda指定虚拟环境下运行jupyter notebook”的问题。

$ conda activate tensorflow   # 'tensorflow'换成你想进入的虚拟环境的名字
$ which ipython
$ which jupyter 

运行这两句，看看你这个虚拟环境是否安装过这二者——若未安装，则显示的路径是全局的路径，比如"/usr/local/bin/jupyter"，而非你家目录下的jupyter路径。

$ conda install jupyter
$ conda install ipython
注意：要在你想安装它们的虚拟环境下运行这两个命令


在base环境中运行
sudo conda install nb_conda

在虚拟环境中运行（添加内核选项）
conda install -n python_env ipykernel

以后在base环境中jupyter notebook 就可以自己选择切换内核。