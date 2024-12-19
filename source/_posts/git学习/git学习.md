---
layout: posts
title: "git学习"
date: 2020-9-5 11:12:11
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "git"
tags:
- "git"
---

# 分布式版本控制器 git <!--more-->

​		开源分布式版本控制系统git，有廉价本地库，但是性能优秀。

​		修改过毕业论文的人都知道，不断的修改会产生多个版本，有时候上一个版本的比后来改的要好一些，用一个版本控制工具来操作会好很多。在工作中，实际的开发是共同协作，更加需要版本控制，如：甲乙同时分别修改项目的上下两个部分，最后合并到一起，形成一个完整的新项目，用版本控制器可以避免被覆盖，实现科学整合。

#### git特点：

​		1、版本控制在本地完成，服务器断网一样可以开发。

​		2、每个客户端都有完整的项目，不易丢失。

#### git工作机制：

​		工作区（写代码）— git add —> 暂存区（临时存储）— git commit提交 —>本地库（历史版本）— push推送 —> 远程库（代码托管中心）

#### 远程库：

​		局域网gitlab。

​		互联网github、gitee。

### git安装

https://git-scm.com/

### git命令

```bash
git config --global user.name 【your name】
git config --global user.email 【***@gmail.com】 
git init
git status
git add 【**】
git rm --cached 【**】
git commit -m 【日志信息】【文件名】
git reflog  /  log
git reset --hard 【版本号】
```

签名：

​		git config --global user.name 【your name】     设置用户签名（你用git的时候设置一次就够了）

​		git config --global user.email 【***@gmail.com】  设置用户签名（你用git的时候设置一次就够了,git不会验证邮箱的真伪。）

​		签名完毕之后在用户的文件夹下 ls -a 可以看见 .gitconfig 文件。签名的作用是用来区分不同的操作者身份。以此确认作品是谁提交的。git首次使用一定要签名，不然无法提交代码。

初始化本地库：

​		git init

​		使用之后，在文件夹下生成 .git 等几个隐藏文件，代表这个文件夹初始化完毕。

查看状态：

​		git status

​		会提示工作区内有哪些待添加的文件，可以用git add 建立添加（追踪）。在工作区的文件是红色，在暂存区的文件是绿色。已经提交（commit） 的文件被修改，若红色提示 modified: 【被修改文件名】则说明修改的文件还在工作区，绿色modified: 【被修改文件名】则说明修改的文件已经add到暂存区了。

建立追踪：

​		git add 【待提交文件】

删除暂存区文件：

​		git rm --cached 【**】

提交暂存区文件：

​		git commit -m 【"日志信息"】【文件名】

​		提交了多个版本的 hello.txt 后，在初始化的文件夹下，也只会有一个hello.txt文件。

```bash
git commit -m  "first commit"  hello.txt
[master（根提交） 3dff95f] first_commit    // 3dff95f 就是版本号。
// 修改 hello.txt
git commit -m  "second commit"  hello.txt
// 在初始化的文件夹下，也只会有一个hello.txt文件。
```

查看日志信息：

​		git reflog

​		git log      

​		git log 可以看到完整的版本控制号。

​		git reflog 可以看到 head->指针 指向的是哪个“日志信息”的版本。可以直接用Linux命令 如 cat 【**】 查看head -> 指针 指向的“日志信息”的版本，直接用Linux命令就行。

版本穿梭：

​		git reset --hard 【版本号/精简版本号】

​		先用git reflog 找版本号，然后版本穿梭 回到之前的某一个版本。

​		这里我们可到初始化的文件夹的 .git 文件夹下，看到 HEAD和refs，HEAD内部 存的是 ref: refs/heads/master 表示head 指向的是主分支，refs 内的heads 文件夹存的是master文件，打开这个master文件可以看到 里面存的是 当前head指针所指向版本的完整版本控制号。

​		版本穿梭原理其实是多个日志文件 存在 内存 中，改变 head / master 指针，来指向内存空间的不同日志文件。



### git分支

​		版本控制中，同时推进多个任务，每一个任务都有单独的分支，使用分支可以把程序员的工作从主线任务中分离出来，开发自己的分支时，不影响主线分支的运行，可以理解为一个副本（但分支的底层也是指针的引用）。

​		例如：主线游戏基础上开发新的英雄角色和武器系统，会在主线分支的基础上，生成两个子分支，一个开发英雄角色，一个开发武器系统，后来只要合并进主分支就行了，这两个分支合并进来之前，游戏还是保持之前的代码继续运行。	

```bash
git branch 分支名    // 创建分支
git branch -v       // 查看分支,可以看见自己现在在哪个分支上。
git checkout 分支名  // 切换分支
git merge 分支名     // 把指定的分支合并到当前分支上
// 一开始只有一个主分支 master
```

#### （代码冲突）

​		合并分支时，两个分支在""同一个文件的同一个位置""，有两套完全不一样的修改，git无法替我们决定用那一个，必须认为决定新代码内容。

​		如何理解 “同一个文件的同一个位置” ？例如：

​		master分支

```bash
hello chen! yes!  
hello chen! yes!  master   // 第二行修改
```

​		hot_fix分支

```bash
hello chen! yes!  hot_fix  // 第一行修改
hello chen! yes!  
```

​		在master分支内 用 git merge hot_fix 合并，会conflict并报致命错误fatal。此时用 git status 查看状态可以看到一个 both modified 提示。

#### （手动合并）

​		在合并代码遇到冲突后，我们可以手动合并，在当前分支下，用vim修改文件即可，vim 打开文件后可以看见：

```bash
<<<<<<< HEAD
hello chen! yes!           // head 和 === 之间是当前分支代码。
hello chen! yes!  master
=======
hello chen! yes!  hot_fix  // === 和 head 之间是要合并进来的分支代码。
hello chen! yes!
>>>>>>> hot_fix
```

​		直接手动删除特殊标识  <<<<<<< HEAD ；======= ； >>>>>>> hot_fix ；手动删除重叠部分，保留想要留下的部分即可。例如将上述代码保留为以下形式：

```bash
hello chen! yes!  hot_fix
hello chen! yes!  master
```

​		修改之后文件 又一次的发生了变化，需要重新 git add 和 git commit 。

​		但是在代码冲突后，

​		// git commit "日志信息"  【文件名】//   

​		这样的命令会报错，会提示 fatal :cannot do a partial commit during a merge. 合并过程中不能做部分提交。需要在合并命令中省略文件名：

​		 // git commit "日志文件"  // 

​		git 合并分支，其实底层也是指针。版本穿梭就像游标卡尺，游标master上下移动；而git分支就像复制出来了多把游标卡尺，由head指针指向不同游标卡尺的游标，来选择用那一个游标卡尺。



### 开发工具 集成 git

​		实际工作中操作git不一定在命令窗口中进行。这里idea只是一个开发工具，换成其它的开发工具也是一样的逻辑。

#### (配置git忽略文件)

​		很多开发工具都会在项目文件夹下生成自己的一些文件，这些文件与实际功能无关，不参与服务器部署运行，把它们忽略可以屏蔽IDE工具之间的差异。

​		创建忽略规则文件***.ignore，为了便于让～/.gitconfig 文件引用，建议放在home目录下。例如建立一个 git.ignore文件内部如下：

```shell
*.class        // class文件全部忽略。
*.log					 // 日志文件全部忽略。
*.jar          // 所有jar包文件全部忽略。
*.zip
*.tar
*.rar
```

​		设置完 git.ignore 忽略规则文件之后，还需要让 .gitconfig 能够找到它，找到自己的 .gitconfig 文件，一般在用户文件夹下，在里面添加如下内容：

```shell
[user]
		name = 你的用户名
    email = 你的邮箱
//以下为新加入内容
[core]
		excludesfile = 你的 git.ignore 文件绝对位置
```

#### (开发工具内定位git程序)

​		先用命令找到git的安装位置

```bash
(base) MacBook-Pro:bin chenyushao$ whereis git 
/usr/bin/git
```

​		在开发工具内找 setting ——> Version control ——> git ——> 把上面用命令找到的git位置添加进去即可，如果开发工具自动检测并添加了git路径 你就不用再重复此步骤。

#### （开发工具内使用git程序）

​		在开发工具内可见上方的 vcs（version control settings版本控制设定），按照以下操作 ：

​		import into version control ——> Creat git repository创建本地库 ——> 选择一个文件夹作为本地库。

​		在选择一个文件夹作为本地库之后，此文件夹内会出现一个隐藏的 .git 文件，其实以上步骤就是 命令窗口的 git.init 初始化操作，只不过是在开发工具内完成的而已。

​		之后的操作和在命令窗口一样，红色表示在工作区，绿色表示在暂存区，黑色表示已经提交。可以一个一个文件追踪 、回滚、提交，也可以选中文件夹一次性全部追踪、提交等等。值得注意的是，之前如果设置了忽略规则文件 git.ignore ，在集体提交时，会问你需不需要把忽略的文件一起提交，要选拒绝，要不然忽略规则文件白设置了。	

​		在开发工具的左下角version control 可以看见log信息，可以看见head指针指向的是哪个分支谁在什么时候提交的，等等信息，可以点击log内的版本信息checkout 直接切换分支，黄色图标代表head指针。

​		版本内合并分支，举例：在master分支上，想要把hot_fix分支合并进master。在右下角的 Local Branches 可见自己处于master分支，选 hot_fix ——>Merge in to Current ，就把本地库的hot_fix分支合并到当前的master分支下了。

​		代码冲突合并，举例：前面正常合并流程，会自动弹出手动merge的选项，出现三个代码的对比，左、右两个自己选一个就行；也可以用 “》” 和 “《” 按钮 把左右的区别代码选择性的移动到中间，点击apply就保留了中间改好的新版本，日志可见已经合并成功。



# Github

### 创建远程库

​		在GitHub页面右上角加号 New repository,选好license 种类等等创建就好了。

​		在code 里可以看见 https和ssh的 链接 ，例如 https://github.com/Tigerpop/Zhejiang-University-algorithm-and-data-structure-by-python-implementation.git 就是一个我的远程库的链接。	

```bash
git remote add 别名 远程地址  //起别名,用以简化远程地址的麻烦输入
git remote -v               //查看当前库的别名

```

#### （团队内协作）

​		初始成员 push 代码上 代码托管中心，另一个成员从代码托管中心 clone代码 下来在其本地库修改，修改完了再push上代码托管中心，初始成员再从代码托管中心 pull 拉取 改好的代码，实现了初始人本地库、修改者本地库、代码托管中心的代码一致。

#### （跨团队协作）

​		大佬不是本团队人员，但是想帮初始人改改代码，于是直接从初始人团队的远程库 fork分叉 了一份副本到大佬自己的远程库（此fork操作在代码托管中心的两个远程库之间完成），大佬再clone代码 到自己的本地库修改，改好之后大佬把代码 push 到大佬自己的远程库，随后向初始人发出pull request 拉取请求（此 pull request 操作在代码托管中心的两个远程库之间完成），经过初始人审核之后，如果初始人满意就可以merge到自己的远程库，然后pull拉取下来到初始人本地库。

### 代码推送 push

​		推送的最小单位是 “分支”，

```bash
git push 远程库别名(或者远程库地址) 推送的分支
```

​		这里经常用代理的同学要注意一下 自己对终端、git是不是设置了代理。然后就是一、要在你的GitHub和电脑终端之间建立正确的ssh链接（建立链接和用不用代理时没有相关性的），二、GitHub账号输密码时要用GitHub的令牌代替（令牌记得勾选全部选项），可以参考我利用hexo搭建个人博客中的介绍。

### 代码拉取 pull

​		远程库发生了更改，本地库和远程库的代码已经不一样了，初始人需要从远程库拉取代码下来，同步本地库与远程库。

```bash
git pull 远程库别名 拉取的分支
```

​		如果 push 出现 报错 

​		 “提示：更新被拒绝，因为推送的一个分支的最新提交落后于其对应的远程分支。 ”

 		导致出现这个的原因是因为本地仓库没有更新远程仓库中的内容。此时需要更新本地仓库，要让每个本地的分支从远程库对应分支 pull拉取代码。例如：

```shell
// 本地的 master分支 从远程库的master 拉取代码。（除了master分支，其它每个分支也要到远程库对应的分支中拉取代码） 
git checkout master
git pull 库别名 master

// 本地库更新完毕，就可以正常推送代码分支了（这里以master为例）。
git push 库别名 master 
```

### 代码克隆 clone

​		clone 不用 git init初始化 ，也不用任何账号。

```bash
git clone 远程库链接
```

​		clone 之后会做一下几件事：

​		1、拉取代码；2、初始化本地库；3、创建别名。

​		一个完整的项目被克隆到了本地库。此时进入远程库别名的文件夹内，再运行查看别名的命令 git remote -v ，我们可以看见 一个别名统一是 origin（clone后统一的规定）。

### 邀请加入团队	

​		某程序员在clone代码并修改完成之后，git add / git commit -m * * 本地库加入版本，随后需要将分支 push 到远程库，会发现 没有权限推送。

​		此时 此程序员 需要被 初始人 邀请加入团队才能获得推送权限。

​		初始人在GitHub进入远程库，点击 Settings ——> Manage access ——> Invite a collaborator ——> 输入被邀请人GitHub的账号名 ——> Add *** to this repository ——> Pending Invite邀请函 ——> 将复制的邀请函发送给此想要加入团队的程序员。

​		此程序员 用微信QQ丁丁啥的接收了 邀请函之后，打开接受即可。接受之后，此程序员的GitHub内可见一个新的 初始人的远程库， 之后就可以正常推送了。

### ssh免密登陆

​		可以参考我之前的 《利用hexo搭建个人博客》中的介绍。push 时，用

```bash 
git push ssh对应远程库链接 分支名  
```

​		以此类推，这里就不赘述了。

### 开发工具 集成 GitHub

​		和开发工具集成git 一样，集成github 也要先在version  control 内 看看有没有GitHub，如果没有GitHub，就在Plugins 安装GitHub即可。登陆账号的时候记得用Use Token GitHub令牌登陆。

​		项目传到远程库的操作：

​		VCS ——> Import into Version Control ——> Share Project on GitHub ——> 起名字

 	   push 操作(二选一)：

​		1、选中本地库的项目或文件——> git ——> Repository ——> Push ——> 

​		2、vcs ——> git ——> Push ——> 

​		（注意）：push推送时，由于我们建立好了ssh的免密登陆，优先使用ssh链接推送，而不是不稳定的https链接，操作如下：

​		点击 远程库的别名 ——> Define Remote ——> 命名后，在url 黏贴远程库复制的ssh链接——> 再次点击 远程库的别名 ——> 选择刚刚添加的ssh链接选项。

​		pull 操作：

​		( push之前一定要保证本地库版本高于远程库，因此，成熟的程序员都会在动手改代码之前，先pull 拉取远程库，再修改、提交、推送。)

​			vcs ——> git ——> Pull ——> 选择ssh链接后 点击pull

​		( pull 拉取远程库代码到本地库之后，如果代码不一致，会自动合并，如果合并失败，会要求手动合并解决代码冲突问题。)

​		clone 操作：

​		在打开开发工具的 第一个界面就可以看见 Get from Version Control 直接clone下来项目。在URL 输入远程库的ssh链接 或者https链接 点击clone即可。或者在vcs 也有Get from Version Control，操作一样。



# Gittee

​		类似于GitHub，这里不再赘述。但是由于在国内，直接用https的链接就行，没必要用ssh了。

### 码云链接GitHub 进行代码复制和迁移

​		在新建仓库之后，往下可以看见一个导入已有仓库的提示，黏贴GitHub远程库的https链接即可。











