---
title: codeforces-14D-Two Paths
date: 2019-09-10 15:56:17
categories: Codeforces
tags: ['DP', 'tree', 'graph']
---

## 描述
给一个无向无环图(`n`个节点, `n - 1`条边, 每条边权重相同). 找到不相交(没有共同节点)的两条路径, 使得这两条路径长度之积最大, 输出这个最大值.


## 思路
枚举所有边, 在本轮断开这条边, 得到两棵树. 分别对两棵树求最大直径. 类似leetcode-124.

## 代码
```python
import collections
def do():
    n = int(input())
    g = collections.defaultdict(set)
    edges = set()
    for _ in range(n - 1):
        x, y = map(int, input().split(" "))
        g[x].add(y)
        g[y].add(x)
        edges.add((x, y))
        
    def count(node, parent):
        l1 = l2 = 0
        if memo[node] == -1:
            res = 0
            for nei in g[node]:
                if nei != parent:
                    tmp = count(nei, node)
                    if tmp >= l1:
                        l2 = l1
                        l1 = tmp
                    elif tmp >= l2:
                        l2 = tmp
                    res = max(res, tmp)
            memo[node] = res + 1
            longest[node] = l1 + l2 # path length, not nodes
        return memo[node]
    
    ans = 0
    for x, y in edges:
        memo = [-1] * (n + 1)
        g[x].remove(y)
        g[y].remove(x)
        longest = [0] * (n + 1)
        count(x, -1)
        l1 = max(longest)
        longest = [0] * (n + 1)
        count(y, -1)
        l2 = max(longest)
        g[x].add(y)
        g[y].add(x)
        # print(x, y, l1, l2)
        ans = max(ans, l1 * l2)
    return ans

print(do())
```