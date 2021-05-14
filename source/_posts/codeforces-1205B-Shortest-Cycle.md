---
title: codeforces-1205B-Shortest Cycle题解
date: 2019-09-04 12:39:15
categories: Codeforces
tags: ['shortest path', 'math', 'brute force', 'graph', 'floyd']
---

## 描述

给一个数组`nums`, 对于里面的任意两个数字`a`和`b`, 如果`a & b != 0`, 说明`a`和`b`是连通的. (每个数字可以看做一个节点) 求这些数字组成的图中, 最小环的长度. 如果没有环, 返回`-1`.
数组长度最大`100000`.

## 思路
如果所有节点两两判断是否连通, 复杂度肯定吃不消. 考虑所有数字(都是64位), **每一位可以看做是用来和其他数字连通的接口**, 
- 比如第一位(最低位), 如果所有数字中, 有`>=3`个数字在这一位是`1`, 那么最小环的长度一定是`3`. 
- 如果对于某一位, 所有数字中, 这一位全是`0`, 那么相当于这个接口没有被用到. 两个数字`a`和`b`就算连通, 也不是因为这一位的贡献. 
- 如果对于某一位, 所有数字中, 只有一个数字在这一位是`1`, 其他全是`0`, 那么也相当于这个接口没有被用到(没有和它匹配的数字).
- 所以, 真正有价值的信息是出现了正好`2`次的位中. 64位中, 就算出现`2`次bit`1`的位置完全分散开, 也只有`64*2=128`个数字. 对于这最多`128`个数字, 可以用floyd算法找最小环. 复杂度`O(n**3)`可以接受.


## 关于WA了一次的floyd
为什么要写成下面这样, 而不能更新完多源最短路径, 再求最小环呢? 
```python
for k in range(nv): # k是中间点, 相当于[i]-----[k]-----[j]~[i](成环)
    for i in range(k):
        for j in range(k):
            if i != j:
                res = min(res, dis[i][j] + mp[j][k] + mp[k][i])
    for i in range(nv):
        if k != i:
            for j in range(nv):
                dis[i][j] = min(dis[i][j], dis[i][k] + dis[k][j])
```
最内层每一次更新`res`和`dis[i][j]`都是针对上一层`k-1`的值来说的. 也就是对于环内节点最大是`k`的环来说, `i`和`j`的距离, 以及最小环长度. 如果完全更新完`dis`矩阵, 那么`dis[i][j]`这条最短路可能包含了当前的`k`, 再和`dis[i][k]`, `dis[k][j]`相加没有意义.

上面其实也是floyd算法求(无向图)最小环的模板代码. (`k`为什么写在最外面?: https://www.zhihu.com/question/30955032)

## 代码
```python
def do():
    n = int(input())
    nums = [int(c) for c in input().split(" ")]
    valid = set()
    for i in range(64):
        count = []
        for j in range(n):
            if (1 << i) & nums[j]:
                count.append(nums[j])
            if len(count) == 3:
                return 3
        if len(count) == 2:
            valid.add(count[0])
            valid.add(count[1])
    # valid won't be that large hahaha at most 64*2
    nv = len(valid)
    valid = list(valid)
    dis = [[float('inf')] * nv for _ in range(nv)]
    for i in range(nv):
        for j in range(nv):
            if i == j:
                dis[i][j] = 0
            elif valid[i] & valid[j]:
                dis[i][j] = 1
    mp = [[_ for _ in dis[i]] for i in range(len(dis))] # 原始的邻接矩阵
    res = nv + 1
    for k in range(nv):   
        for i in range(k):
            for j in range(k):
                if i != j:
                    res = min(res, dis[i][j] + mp[j][k] + mp[k][i])
        for i in range(nv):
            if k != i:
                for j in range(nv):
                    dis[i][j] = min(dis[i][j], dis[i][k] + dis[k][j])
    return res if res <= nv else -1
 
print(do())
```


 
