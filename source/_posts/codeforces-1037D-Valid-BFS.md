---
title: codeforces-1037D-Valid-BFS
date: 2019-09-08 22:35:40
categories: Codeforces
tags: ['bfs', 'graph']
---

## 描述
给一个任意的树(任意的无向无环图), 和一个节点序列. 判断这个序列是否是一种以节点`1`为入口的BFS序列. 

## 思路
层序BFS模拟一遍. 有趣的一点是, 本题我们不记录节点的`childen`, 而是记录他们的`parent(s)`. 对于当前层中的每个点, 检查他们的`parents(last)`是不是有序的. 如果`parents(last)`一直有序并且走到头, 那么另起一层, 上层的节点们作为新的`parents(last)`. 如果提前失配, `last`会是空的, 直接return.
 


## 代码
```python
import collections
def do():
    nodes = int(input())
    parent = collections.defaultdict(set)
    for _ in range(nodes - 1):
        x, y = map(int, input().split(" "))
        parent[y].add(x)
        parent[x].add(y)
    seq = [int(c) for c in input().split(" ")]
    if seq[0] != 1:
        return "NO"
    last = [seq[0]]
    cur = 0
    i = 1
    while i < len(seq):
        if not last:
            return "NO"
        level = []
        while i < len(seq):
            if last[cur] in parent[seq[i]]:
                level.append(seq[i])
                i += 1
            else:
                cur += 1
                if cur == len(last):
                    last = level
                    cur = 0
                    break
    return "YES"

print(do())
```