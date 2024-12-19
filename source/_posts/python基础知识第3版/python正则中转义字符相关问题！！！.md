---
layout: posts
title: python正则中转义字符相关问题！！！
date: 2022-11-11 22:37:07
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "python基础知识第3版"
tags:
- "正则"
- "转义字符"
---


# python正则中转义字符相关问题！！！

```python
import re

b = '\\d'
print(b)  # 返回 \d
b = r'\\d'
print(b)  # 返回 \\d
'''
>>> b = r'\\d'  # 终端内 可见 ：直接 输出b 和 print(b) 的结果不一样！！！
>>> b           # 因为print一次就用到了 python 3.7 的解释器，进行了一次转义。
'\\\\d'
>>> b = '\\d'
>>> b
'\\d'
'''
# (一)
b = r'\\d'
print(b)  # 返回 \\d
print(re.sub(b,'ok','00\d00')) #  返回 00ok00
# (二)
a = '\\d'
print(a)  # 返回 \d
print(re.sub(a,'ok','00\d00')) #  返回 okok\dokok

案例解释：

（一）r'\\d' 字符串前的这个 r 表示python 在r修饰的字符串过 python解释器时 不被转义；
		只会在通过 正则解释器 时 被转义一次，\\d 被 转义为 \d ，以 \d 形态进入正则层！！！
		在正则层内，正则匹配过程直接把 所有的 \d 与之匹配;

（二）'\\d' 字符串，\\d 过python解释器时 已经被转义为 \d，
		过正则解释器时 \d 被转义为 "数字" ，以"数字"形态进入正则层！！！
		正则层内，正则匹配过程直接把 所有"数字" 与之进行匹配。
```

## 如何理解 python的 转义字符？

可以把字符串进入正则过程，分为为三层：
1、字符串层
   （通过python解释器后，才能进入python层，过解释器的时候`'\\'`会被转义成`'\'`）

2、python层
   （通过正则解释器后，才能进入正则流程，过正则解释器的时候 `'\\'`又会被转义成 `'\'`）
3、正则层
      (终于可以开始正则匹配了！)



## 一道 计算过程中， `\\` 正则化 变 `\\\\`的案例题。

​		本题来源于 牛客华为HJ74 参数解析

https://www.nowcoder.com/practice/668603dc307e4ef4bb07bcd0615ea677?tpId=37&tqId=21297&rp=1&ru=/exam/oj/ta&qru=/exam/oj/ta&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D2%26tpId%3D37%26type%3D37&difficulty=undefined&judgeStatus=undefined&tags=&title=

## 描述

在命令行输入如下命令：

xcopy /s c:\\ d:\\e，

各个参数如下：

参数1：命令字xcopy

参数2：字符串/s

参数3：字符串c:\\

参数4: 字符串d:\\e

请编写一个参数解析程序，实现将命令行各个参数解析出来。



解析规则：

1.参数分隔符为空格
2.对于用""包含起来的参数，如果中间有空格，不能解析为多个参数。比如在命令行输入xcopy /s "C:\\program files" "d:\"时，参数仍然是4个，第3个参数应该是字符串C:\\program files，而不是C:\\program，注意输出参数时，需要将""去掉，引号不存在嵌套情况。
3.参数不定长

4.输入由用例保证，不会出现不符合要求的输入

数据范围：字符串长度：1\le s\le 1000\1≤*s*≤1000 

进阶：时间复杂度：O(n)\*O*(*n*) ，空间复杂度：O(n)\*O*(*n*) 

```python
# 我在这里剑走偏锋，用python的re正则，非常简单的解决了问题；
# 首先是正则把 “ ” 内容 放进一个list中备用，并把原str 中 “ ” 内容用一个 已用的标记占住。 
# 再用空格切分str 为 list ，遍历此 list，遇到 占位标记 就从 备用list 中取值填过来即可。
# 这可能是 最简单的 解法了，借助正则化跳过了 复杂的算法。
import re
a = input()
pattern = r'".*?"'
change_a = re.sub(r'".*?"','used',a)           
temp_list = re.findall(pattern,a)
# 虽然在过程中 转义字符\ 会显示变 \\,但是后面输出会自动复原！！！
# print(temp_list) # 会显示 ['"c:\\\\"', '"d:\\\\e"'] 没关系，输出temp_list.pop(0)[1:-1] 会自动复原。
a = change_a.split(' ')
print(len(a))
for i in a:
    if i=='used':
        print(temp_list.pop(0)[1:-1])
    else:
        print(i)
```


