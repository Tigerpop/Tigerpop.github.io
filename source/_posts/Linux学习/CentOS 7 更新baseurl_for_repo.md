---
layout: posts
title: "CentOS 7 更新baseurl_for_repo"
date: 2024-12-25 12:23:07
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Linux"
tags:
- "yum"
- "centos"

---

简介 <!--more-->



在centos7 中运行 yum 相关操作遇到报错

```sh
[CentOS 7 Cannot find a valid baseurl for repo: base/7/x86_64 when I run yum update]
```


其实就是 centos7 太老了。

解决方法

```sh
sudo cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak

sudo vim /etc/yum.repos.d/CentOS-Base.repo

# And comment out all the mirrorlist lines.
# And uncomment all the baseurl lines.
# For CentOS version 8 and below you will also need to change `mirror.centos` to `vault.centos` in the baseurl lines as they are deprecated.

# 就是注释掉mirro 列，打开 baseurl列，然后把mirror.centos都改成 vault.centos。
```



> 参考：
>
> https://superuser.com/questions/1373881/centos-7-cannot-find-a-valid-baseurl-for-repo-base-7-x86-64-when-i-run-yum-upda