---
layout: posts
title: "Mac终端用代理_git-cloing用代理_brew换源"
date: 2022-10-29 15:42:07
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "Mac"
tags:
- "Mac"
- "git"
- "brew"
- "换源"
---​	

参考：<!--more-->
https://www.zhihu.com/question/27159393

https://kerminate.me/2018/10/22/mac-%E7%BB%88%E7%AB%AF%E5%AE%9E%E7%8E%B0%E7%BF%BB%E5%A2%99/



********************************************************************

```shell

Mac 终端用代理（默认shell是bash）：
方法一：
每次用之前输入以下内容 生效，影响面很小（只对当前终端有效）。
export http_proxy=http://127.0.0.1:自己的代理端口
export https_proxy=https://127.0.0.1:自己的代理端口
socks5代理则：
export http_proxy=socks5://127.0.0.1:自己的代理端口
export https_proxy=socks5://127.0.0.1:自己的代理端口

方法二：
也可以把终端代理写进.bash_profile，就不用每次打开终端输入上面的内容了。
vim ~/.bash_profile
改写为
function proxy_on(){
    export http_proxy=http://127.0.0.1:1087
    export https_proxy=http://127.0.0.1:1087
    echo -e "已开启代理"
}
function proxy_off(){
    unset http_proxy
    unset https_proxy
    echo -e "已关闭代理"
}
运行生效
source ~/.bash_profile
以后每次只需要 proxy_on 或者proxy_off 就可以切换是否代理了。

Mac 终端用代理（升级系统后，默认的shell不再是bash，而是zsh）

方法一：重新回到bash版本的shell 中； 
       echo "$SHELL" # 查看自己的shell版本，可以用 chsh -s /bin/bash 或者 cash -s /bin/zsh 来选择用哪个shell，新开一个终端就是新指定的shell 了。

方法二：配置zsh 的代理

where proxy 因为我的clashX 端口是7890

proxy () {
  export http_proxy="http://127.0.0.1:7890"
  export https_proxy="http://127.0.0.1:7890"
  echo "开启代理HTTP Proxy on"
}

where noproxy

noproxy () {
  unset http_proxy
  unset https_proxy
  echo "关闭代理HTTP Proxy off"
}
以后每次只需要 proxy 或者 noproxy 就可以切换是否代理了。

********************************************************************


git cloning 用代理，不推荐用全局代理，因为会让访问国内源太慢，推荐只有访问GitHub时用代理。

若是 socks5代理的方法，只对访问GitHub时用代理：
git config --global http.https://github.com.proxy socks5://127.0.0.1:自己的代理端口
git config --global https.https://github.com.proxy socks5://127.0.0.1:自己的代理端口

（如果想搞全局代理的话，非socks5情况）
git config --global http.proxy http://127.0.0.1:自己的代理端口
git config --global https.proxy https://127.0.0.1:自己的代理端口
（如果想搞全局代理的话，socks5情况）
git config --global http.proxy socks5://127.0.0.1:自己的代理端口
git config --global https.proxy socks5://127.0.0.1:自己的代理端口

检验是否成功使用代理 （Clash或者trojan记得选全局模式，不然还是会看见是国内ip）
curl cip.cc 

取消代理，复原：
git config --global --unset http.proxy
git config --global --unset https.proxy

********************************************************************

参考：
https://blog.csdn.net/weixin_35757704/article/details/112133562
https://blog.csdn.net/qq_43213352/article/details/104343627

brew 换清华和中科大源，（值得一提的是，如果终端用了代理，访问国内源的意义就不大了，还可能更慢，应视情况而定）：

替换brew.git:

$ cd "$(brew --repo)"

中国科大:

$ git remote set-url origin https://mirrors.ustc.edu.cn/brew.git

清华大学:

$ git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git

替换homebrew-core.git:

$ cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"

中国科大:

$ git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git

清华大学:

$ git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git

替换homebrew-bottles:

中国科大:

$ echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile
$ source ~/.bash_profile

清华大学:

$ echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles' >> ~/.bash_profile
$ source ~/.bash_profile

应用生效:

$ brew update


···············································································
（若：你之前折腾过不少导致你的Homebrew有点问题，那么可以尝试使用如下方案：）

诊断Homebrew的问题:

$ brew doctor

重置brew.git设置:

$ cd "$(brew --repo)"
$ git fetch
$ git reset --hard origin/master

homebrew-core.git同理:

$ cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
$ git fetch
$ git reset --hard origin/master

应用生效:

$ brew update 
···············································································


（某些时候也有换回官方源的需求）

重置brew.git:

$ cd "$(brew --repo)"
$ git remote set-url origin https://github.com/Homebrew/brew.git

重置homebrew-core.git:

$ cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
$ git remote set-url origin https://github.com/Homebrew/homebrew-core.git


···············································································
（若：Mac homebrew报错Error: homebrew-core is a shallow clone.）
则：
	删除homebrew-core后更新
	cd /usr/local/Homebrew/Library/Taps/homebrew
	rm -rf homebrew-core
	brew upgrade

***************************************************************************

如果实在是下载不下来某个文件：
如果brew install 工具时下载错误，可以直接从浏览器下载，然后放到下面的目录就可以了。

brew 下载的位置在
/Users/chenyushao/Library/Caches/Homebrew


```

