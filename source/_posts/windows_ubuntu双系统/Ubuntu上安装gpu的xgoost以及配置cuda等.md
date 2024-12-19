---
layout: posts
title: Ubuntu上安装gpu的xgoost以及配置cuda等
date: 2022-10-29 23:10:16
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "windows_ubuntu双系统"
tags:
- "ubuntu"
---

简介 <!--more-->


```shell


#########################################################################################
ubuntu GPU 版本安装：
1.首先按Ubuntu的版本号安装好nvidia显卡驱动。就在“软件和更新”装好就行了。



2.按Ubuntu版本号安装cuda。参考资料：https://blog.csdn.net/ashome123/article/details/105822040
        卸载旧的cuda。
	cd /usr/local/cuda/bin
	sudo ./cuda-uninstaller
	
	nvidia-smi 查看支持的cuda版本，我这是cuda10.2。
	去cuda官网按本电脑支持的版本下载cuda，Ubuntu20.4就选ubuntu18.4的cuda就行了
	gcc降级到gcc7，
	update-alternatives  就行gcc版本切换，切换至gcc7.
	（关键）注意默认的python版本，如果是python3.8.2，python3.8.2对应gcc编译器是			gcc9.3.0，版本超过了gcc8，cuda安装后无法编译成功，此时应该再安装一个python3.7.0并将其设定为默认	python，因为python3.7.0内部gcc是gcc7.2.0.。
	
	去安装cuda。
	有一个未安装相关驱动警告，说没有完整安装cuda，没有关系，因为我们用了自己的驱动。

	
3.安装cudnn。参考资料：https://blog.csdn.net/ashome123/article/details/105822040


3.1 安装anaconda3-5.3.1-linux-x86_64.sh     在：  https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/
				     	参考：https://zhuanlan.zhihu.com/p/32925500


4.安装tensorflow-gpu
	参考：https://zhuanlan.zhihu.com/p/84940320
	用conda安装与cudnn、cuda配套的tensorflow版本
	进入conda的用python3.7的环境，然后用pip（此pip对应的python版本也为3.7）安装tensorflow2.1.0。
	tensorflow2.1.0 gpu/cpu版本合并了。
	先进入conda 建好的环境，然后使用tensorflow就好了。

5.安装xgboost
	新建一个python3.7的环境，在里面用conda命令，conda install py-xgboost-gpu  即可。

##########################################################################################


########################################################################################
Mac CPU版本安装

直接安装anaconda，python3.7的那个anaconda版本。

新建一个虚拟环境 xgb，conda create -name xib python=3.7 ； pip install xgboost 。

新建一个虚拟环境 tf2，pip install tensorflow=2.0.0 -i [清华源]。

注意在两个虚拟环境中检查 是下载到 pip3 里面的还是下载到 pip 里面的。在两个虚拟环境中 分别 
pip3 list ，pip list

如果import tensorflow as tf 找不到包，说明在另外一个pip/pip3 的包里，
例如我用pip3 下载的tensorflow找不到包
此时 pip3 uninstall tensor flow 
再  pip install tensorflow=2.0.0 -i [清华源]。

#######################################################################################



```

