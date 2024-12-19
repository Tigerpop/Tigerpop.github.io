---
layout: posts
title: "Hexo配合GitHub简易建立博客"
date: 2020-9-5 11:12:11
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "博客制作"
tags:
- "hexo"
- "git"
---



---
typora-root-url: ../../../tiger_pop_hexo/source

---

# 一、首先 安装工具：最新的稳定版本 nodejs、npm 和 git 。  

```shell
	# 一些情况下npm集成在nodejs环境中不需要再装。Mac用户Xcode已经集成了git，找到 Xcode -> 偏好设置 -> locations -> commend line tools，有显示就是git已经有了。

	# 注意：nodejs的版本尽可能高一点，如果用brew 直接安装的node记得升级，不然后面可能会遇到不必要的麻烦，

	# 升级nodejs：先看看版本 node -v ； sudo npm cache clean -f  ；sudo npm install -g n；sudo n stable； 验证一下 node -v；npm -v；
```



# 二、用ssh连上github。

```shell
	# ssh-keygen -t rsa -C "你的github注册邮箱"

	# 按照提示选择一个地方放置 .ssh文件，里面有私钥和公钥。我们接下来要用到的就是这个公钥 .ssh/id_rsa.pub 

	# 将id_rsa.pub 内容拷贝，打开并登陆自己的GitHub，在 setting中找到 SSH and GPG keys 位置 新增 ssh keys，黏贴之前拷贝内容 。

	# 检查是否成功： ssh -T git@github.com ; 按照提示来操作。
```



# 三、利用hexo在本地生成博客。

```shell
	# npm install hexo-cli -g 安装hexo。

	# hexo init [你想取的名字]。    最好先用git代理，如何git代理可以看我其他文档。注意看hexo init是否成功。

	# 进入根目录添加依赖 npm install hero-deployer-git --save ;再进入刚刚生成的[你想取的名字]文件夹内。

	# 本地检查一下，hexo g ; hexo s ; 打开链接。

	# 如果想换一个 theme，可以在hexo网站找一个自己喜欢的类型下载，如[master]，并放到 [你想取的名字]文件夹/theme 内，然后在[你想取的名字]文件夹 将 _config.yml内 theme：修改为你刚才下载的 [master]，具体步骤在每个主题的github下面一般有指导说明。

	# hexo clean ; hexo g ; hero s;检查页面theme是否改成功了。

	# 修改博客的内容。可以看 hexo 写作的说明文档https://hexo.io/zh-cn/docs/writing ，但是我建议直接typora 编辑完了 直接复制或者拖进来。
	
	# 关于图片 显示 找路径的问题：
	# 在 hexo 的根目录下 用 npm 按照依赖 
	npm install https://github.com/CodeFalling/hexo-asset-image --save
	# 然后把hexo根目录下的_config.yml中的post_asset_folder设为true，这个配置的意思是每次new post一个博客，会增加一个和博客同名的文件夹用来储存图片。在 typora 的偏好设置中，把<图像设置> 设定为 <复制图片到 ./${filename}.assets 文件夹>设置好typora图片的相对路径；然后在旧有的 typora md文件中选<格式> <复制所有图片到...> 会新建一个文件夹里面存typora 的引用图片，把这个文件夹的图片都复制到 hexo下source下的同名文件夹中（必须直接在这个文件夹下，不能嵌套），即可正常显示图片。
	# 要调的初始页面的东西都在主题 theme 文件夹内，theme文件夹下的_config.yml 就是调整博客此主题布局等等的内容。
	# 写作步骤注意事项：
			1、先在theme下的 _config.yml 看看 有哪些page需要新建出来，根据这写要求的page名新建对应的page；
			2、分类categories 和 标签tags 在 front_matter表头信息中要 额外 写上 type: 类型（分类最好就写categories），以及 layout: "刚刚上面写的类型"，如果没有在表头中设置 layout布局 之后网页的 对应布局的按钮点击就不会有反应。
			3、每次新建一个博客文章post，都要记得在front_matter写上分类、标签信息像下面这样：
categories: 
- Shell
tags:
- bash
- zsh
	
	# hero new < > 即可，有三种new的文件，一种是默认的post ，一种是page用来跳转到新的页面 ，一种是draft自己打草稿用 。生成文件在 source文件夹里面 。

	# 如果插入一个新的page ,让页面可以跳转 ，记得要在 点击页面里面 的md文件内把 相应的跳转页面写好。比如name：/download/ ，以此类推。可以看官网文档。
```



# 四、发布deploy部署博客到互联网。

```shell
# ssh -T git@github.com  先ssh 登陆github。

 #获取github密码令牌： 在github 的setting中 找到 Developer setting ,Personal access tokens ;生成自己的 “个人 通行 代币”（密码令牌）。一定要记得把 令牌的 所有权限打开，不然令牌过了也没用。



# 在GitHub建立一个名为[你的GitHub名.github.io]的仓库。                    



# 在 [你想取的名字]文件夹/_config.yml 内修改： 找到 部署deploy: 下方改为： type: git 

​	repository: 刚刚建立的仓库地址.git 。例如 https://github.com/Tigerpop/Tigerpop.github.io.git
​	branch: main     # 之前也有人用别的，我没有分支，所以直接用main。

	# hexo d ;  部署。

	# 会提示要 输入 你的email信息，或者是GitHub名字， git config --global "你的GitHub注册邮箱" 等等，看提示输入即可。

	# 注意： 在要你输入 github密码的时候，不是输入你的 github 密码，而是输入密码令牌！！！

	# 部署成功后，在GitHub新建立的 [你的GitHub名.github.io]仓库 ，找到setting ，下滑到 GitHub pages，点击Your site is published at 后面的网址，别人访问这个网址就可以看见你的博客了。
```



补充：

​		可以用下载 toc 插件的方式， 加`<!-- toc --> ` 来实现加文章自动识别md语法目录的功能；

​		但是，我更建议的还是直接找一个支持 自动识别md语法目录的 主题，比如next之类的 themes主题，可以省很多事；

​		值得注意的是，用了 next 主题后，如果之前 下载了 toc 插件可能会导致 与 next主题的 目录点击后跳转 的功能冲突，要提前卸载 toc 插件。

```shell
cnpm uninstall hexo-toc  # 国内的卸载
npm uninstall hexo-toc   # 国外的也卸载
```

​		hexo clean -> hexo g -> hexo s 可以看见 目录有了点击后跳转的功能。 

