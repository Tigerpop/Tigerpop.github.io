---
layout: posts
title: HJ65查找两个字符串a,b中的最长公共子串
date: 2022-11-25 20:21:51
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "leecode刷题复习"
tags:
- "动态规划"
---

```python
# 非常经典的一道题，可以暴力n**3 遍历解题。
# 试一试 动态规划解法
# dp[i][j] 为 a 到i b 到j ，最长公共子串长度.
# 当 a[i]==b[j]，更新 递推公式 dp[i][j] = max(dp[i][j],dp[i-1][j-1]+1)
# 初始化 dp 为 0 。
# 注意: 遍历如果从 1 开始，会导致 字符串的比较会跳过 0 号位置的比较。所以我们统一往后平移一位,0到1的位置，以此类推， 而比较的位置 就从 i和j 变成了 i-1和j-1。
a,b = input(),input()
if len(a)>len(b):   # 保证 a 是 短串
    temp = a
    a = b
    b = temp 
dp = [[0 for _ in range(len(b)+1)] for _ in range(len(a)+1) ]      # 初始化
max_dp = 0 
x,y = 0,0
for i in range(1,len(a)+1):
    for j in range(1,len(b)+1):
        if a[i-1]==b[j-1]:
            dp[i][j] = max(dp[i][j],dp[i-1][j-1]+1)
            if dp[i][j]>max_dp:                
                max_dp = dp[i][j]
                x,y = i-1,j-1  
# print(max_dp,x,y) 
print(a[x-max_dp+1:x+1])        
```

