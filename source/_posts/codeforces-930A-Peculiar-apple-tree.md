---
title: codeforces-930A-Peculiar apple-tree[流量]
date: 2019-09-10 16:02:35
categories: Codeforces
tags: ['stream', 'tree', 'graph', 'bfs']
---
## 描述
又是一个讲故事的题...  

本质是有一棵(颠倒的)树, 除了根节点, 每个节点有(唯一一个)子节点. 初始时刻, 每个节点都有一颗苹果, 然后每过一个时间单位, 所有苹果都会坠落到它的子节点. 如果同时有`n`个苹果坠落到同一个节点, 那么这些苹果会两两抵消(消失). 也就是`n`为偶数, 结果是`0`,否则为`1`. 问最终(从根节点)可以收集到多少个苹果.

## 思路
所有苹果同步下降, 对于每个节点, 我们只关心它的所有父节点的贡献. 这样可以bfs层序遍历一下, 统计每层的苹果数量, 奇数对应`1`, 偶数对应`0`.

## 代码
```python
import collections
def do():
    n = int(input())
    nums = [0] + [int(c)-1 for c in input().split(" ")]
    g = collections.defaultdict(list)
    for i, j in enumerate(nums):
        if i != j:
            g[j].append(i)
    cur = [0]
    res = 0
    while cur:
        res += len(cur) % 2
        next = []
        for c in cur:
            for nei in g[c]:
                next.append(nei)
        cur = next
    return res

print(do())
```