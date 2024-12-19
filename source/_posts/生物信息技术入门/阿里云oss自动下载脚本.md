---
layout: posts
title: 阿里云oss自动下载脚本
date: 2022-12-03 11:27:57
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "生物信息技术入门"
tags:
- "oss"
---

# 简易脚本（有点问题）

```shell
#!/bin/bash
# 先还是要用 ossutil64 config 来确定一下下载配置，一般回车用默认的位置即可，也可以自己指定位置，若自己指定位置，使用ossutil64 时要 -c 指定配置文件位置。
# 如果内容重复可能会出现 报错，但是不影响使用。 

read -p "请输入oss地址" oss
read -p "新建一个要存放的位置" path

auto_download() {
        ossutil64 ls $oss | awk -F " " '{print$8}' > ./temp_download
        while  read row
        do
                ossutil64 cp -r $row $path
        done < ./temp_download
}
auto_download
```

# 改良脚本（用这个）

```shell
#! /bin/bash
# 还是需要先ossutil64 config 配置一下，我在脚本中没有把配置过程写进来。

read -p "请输入oss地址" oss
read -p "新建一个要存放的位置" path

auto_download() {
        ossutil64 ls $oss | awk -F " " '{print $(NF)}' | grep "^oss://.*[/]$" > ./temp_dir_download
        ossutil64 ls $oss | awk -F " " '{print $(NF)}' | grep "^oss://.*[^/]$" > ./temp_file_download
        # 读oss路径，并放在临时文件夹(我们这里把文件和文件夹分开处理)。     
        while  read row 
        do
        ossutil64 cp $row $path/            # 注意一下这里用 /表示是文件夹。
        done < ./temp_file_download

        while read row 
        do
        oldIFS=$IFS
        IFS="/"
        array=($row)
        dir=${array[-1]}
        IFS=$oldIFS
        newpath=$path"/"$dir                # 直接存成文件形式，文件名就是$dir 
        ossutil64 cp -r $row $newpath
        done < ./temp_dir_download
}
auto_download
```

# 注意事项

shell 编程中一旦出现 `bad substitution` 报错，只有两种可能：

1、${}  和  $() 用错了，前者是变量，后者是命令。

2、bash 没有使用上，除了在脚本中写 #! /bin/bash ,还要 `bash    XXXX.sh` 而不能` sh     XXXXXX.sh` 因为sh不一定真的用上的是bash。卡了好久，干。

