---
layout: posts
title: 在服务器安装华三castools工具遇到的问题
date: 2024-10-16 15:32:10
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "装机"
tags:
- "castools"
- "包冲突"
---

# 背景

正常情况下，我在登录虚拟机并且在网页前端登录用户，挂载光驱之后，df -h 可以看见出现一个光驱文件夹。

![1729043550725](%E5%9C%A8%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%AE%89%E8%A3%85%E5%8D%8E%E4%B8%89castools%E5%B7%A5%E5%85%B7%E9%81%87%E5%88%B0%E7%9A%84%E9%97%AE%E9%A2%98/1729043550725.jpg)

![1729043674042](%E5%9C%A8%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%AE%89%E8%A3%85%E5%8D%8E%E4%B8%89castools%E5%B7%A5%E5%85%B7%E9%81%87%E5%88%B0%E7%9A%84%E9%97%AE%E9%A2%98/1729043674042.jpg)

当然，如果你的系统是server版本可能没有前端显示，就需要手动挂载一下光驱，方法就和挂载磁盘一样的就是添加一个设备而已。

# 问题

进入这个目录 ，安装 CAStools 工具，遇到报错

`sudo bash CAS_tools_install.sh Selecting previously unselected package qemu-ga.(Reading database ... 175535 files and directories currently installed.)Preparing to unpack .../qemu-ga-10.1.6.2-0ubuntu13_amd64.deb ...Unpacking qemu-ga (10.1.6.2) ...dpkg: error processing archive /media/jlsf/agenttools/linux/x86/castools/qemu-ga-10.1.6.2-0ubuntu13_amd64.deb (--install): trying to overwrite '/usr/sbin/qemu-ga', which is also in package qemu-guest-agent 1:4.2-3ubuntu6.29dpkg-deb: error: paste subprocess was killed by signal (Broken pipe)Processing triggers for systemd (245.4-4ubuntu3.20) ...Errors were encountered while processing: /media/jlsf/agenttools/linux/x86/castools/qemu-ga-10.1.6.2-0ubuntu13_amd64.deb`

![12](%E5%9C%A8%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%AE%89%E8%A3%85%E5%8D%8E%E4%B8%89castools%E5%B7%A5%E5%85%B7%E9%81%87%E5%88%B0%E7%9A%84%E9%97%AE%E9%A2%98/12.png)

这个报错说 qemu-ga 包和 这个脚本里面的包冲突了，我们不能再用原来的 卸载 qemu-ga 再apt-get更新以后装一个解决 ，因为这个脚本用的只能是旧的版本。



# 解决方法

```sh
sudo apt-get remove --purge qemu-guest-agent # 或者 sudo apt-get remove --purge qemu-ga

# 如果想强制安装 qemu-ga 而不卸载 qemu-guest-agent，可以使用 dpkg 的 --force-overwrite 选项来强制覆盖冲突的文件，但是我是先卸载了，然后再强制安装解决问题的。
sudo dpkg -i --force-overwrite /media/jlsf/agenttools/linux/x86/castools/qemu-ga-10.1.6.2-0ubuntu13_amd64.deb

sudo bash CAS_tools_install.sh
```

![1](%E5%9C%A8%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%AE%89%E8%A3%85%E5%8D%8E%E4%B8%89castools%E5%B7%A5%E5%85%B7%E9%81%87%E5%88%B0%E7%9A%84%E9%97%AE%E9%A2%98/1.png)