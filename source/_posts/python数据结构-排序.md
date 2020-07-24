---
title: python数据结构-排序
date: 2020-05-14 11:29:02
tags: [python,数据结构]
categories: python数据结构
---

```python
# 冒泡排序 时间复杂度 n方
def mystor(num):
    for i in range(len(num)-1): # 一共需要遍历整个列表的 n-1 次
        is_stor = False  # 记录本次循环是否发生变化,如果没有变化就可以直接退出
        for j in range(len(num)-i-1): # 每次都要少一共部位位置
           """
这里这个j呢就是控制每一次具体的冒泡过程，请你想一想，我们第一次冒泡需要冒几次，也就是说需要比较几次，
假如有三个数，那只需要两次就可以了，当下一次时，最后一个
已经是有序的了，所以说少冒泡一次，所以这里j每次都会减去i的值，即不用冒“无用之泡泡”
"""    
            if num[j] > num[j+1]:
                num[j],num[j+1] = num[j+1], num[j]
                is_stor = True  # 修改变量，说明发生变化
        if not is_stor: # 没有变化退出
            return num
    return num
mystor([1,6,3,5,5,4])
```



```python
# n平方  每次拿出一个最小的
n = [3,6,5,7,0,4,1,2,10,6,3]
x = len(n)
# 从小到大
for i in range(x): # n次
    mid_id = i
    for j in range(i+1,x):
        if n[j] <  n[mid_id]: # 只需要改变一下判断方向
            mid_id = j
    n[mid_id],n[i] =n[i], n[mid_id]
print(n)
```



```python
# 插入排序  时间复杂度 n方
def mystor(num):
    n = len(num)  
    for i in range(1,n):
        cur = num[i]
        j = i - 1
        while j>=0:
            if cur < num[j]:  # 对比谁大谁小
                num[j+1] = num[j]  # 将 大的数据后移 动
                num[j] = cur  # 将小的数据 插入
            j -= 1
    return num
mystor([1,6,3,5,5,4])
```



```python
# 归并排序   分治思想 时间复杂度 nlogn
def merge(a,b):
    c = []
    h = j =0
    while j < len(a) and h<len(b):
        if a[j] < b[h]:
            c.append(a[j])
            j+=1
        else:
            c.append(b[h])
            h += 1
    if j == len(a):
        for i in b[h:]:
            c.append(i)
    else:
        for i in a[j:]:
            c.append(i)
    return c
def main(listl):
    if len(listl) <=1:
        return listl
    mid = len(listl)//2
    left = main(listl[:mid])
    right = main(listl[mid:])
    return merge(left,right)
main([1,6,3,5,5,4])    
```



```python
# 快速排序  分治思想 时间复杂度 nlogn
def quick_sort(alist,start,end):
    if start >= end:
        return alist
    i= start
    j = end
    mid = alist[start]
    while i < j:
        while i < j and alist[j] >= mid:
            j -= 1
        alist[i] =  alist[j]
        while i < j and alist[i] < mid:
            i += 1
        alist[j] =  alist[i]
    alist[i] = mid
    quick_sort(alist,start,i-1)
    quick_sort(alist,i+1,end)
    return alist
quick_sort([1,6,3,5,5,4],0,len([1,6,3,5,5,4])-1)    
```



