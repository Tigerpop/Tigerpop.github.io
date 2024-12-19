---
layout: posts
title: "Mac连接不上手机热点解决办法"
date: 2022-10-29 15:42:07
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Mac"
tags:
- "Mac"
---​	

    关闭Wi-Fi在Finder当中执行Command+Shift+G快捷键，并输入路径“/Library/Preferences/SystemConfiguration/”在开启的文件夹当中删除下面这5个文件，不放心可以先备份(没有的文件就略过)

com.apple.airport.preferences.plist 
com.apple.wifi.message-tracer.plist 
NetworkInterfaces.plist 
preferences.plistcom.apple.network.identification.plist (我没有这个文件)重启，连接你的热点



作者：二智少女
链接：https://www.zhihu.com/question/30709619/answer/707646178
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。