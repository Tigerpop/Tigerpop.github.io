---
layout: posts
title: "k路归并排序"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "浙大算法与数据结构课程(python实现)"
tags:
- "归并"
- "排序"
---

```python
# coding:utf8

def recursion(nums,l,r):
    # 递归基线条件
    if l==r: # 只有一个元素。
        print([nums[l]])
        return [nums[l]]

    # 递归 (分治)
    mid = (l+r)>>1
    left = recursion(nums,l,mid)
    right = recursion(nums,mid+1,r)
    
    # 合并 完交给上一层
    print(left,right)
    print(merge(left,right))
    return merge(left,right)

def merge(left,right):
    l_n =len(left)
    r_n =len(right)
    i = j = 0
    ans = []
    while i<l_n and j<r_n :
        if left[i] <= right[j]:
            ans.append(left[i])
            i += 1
            if i == l_n:
                ans += right[j:]
                break
        if left[i] > right[j]:
            ans.append(right[j])
            j += 1
            if j==r_n:
                ans += left[i:]
                break
    return ans

def k_road_MergeSort(nums,l,r):
    if nums==None or len(nums)==1:
        return nums
    return recursion(nums,l,r)

if __name__ == '__main__':
    list_ = [7,4,2,1,5,6,9,0]
    list_ = k_road_MergeSort(list_,0,len(list_)-1)
    print(list_)
```

