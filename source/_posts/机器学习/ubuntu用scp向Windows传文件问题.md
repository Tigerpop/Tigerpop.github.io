---
layout: posts
title: ubuntu用scp向Windows传文件问题
date: 2024-9-29 14:32:10
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "windows_ubuntu双系统"
tags:
- "scp"

---



```sh
scp -r ./env_check/ cys@10.5.6.240:\C:\.  # 注意 / 和 \ 方向不一样。
```

这样就会有一个文件夹在 Windows的 c盘下的cys中。

![image-20240928230015507](ubuntu%E7%94%A8scp%E5%90%91Windows%E4%BC%A0%E6%96%87%E4%BB%B6%E9%97%AE%E9%A2%98/image-20240928230015507.png)