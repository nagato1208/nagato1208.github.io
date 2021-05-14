---
title: codeforces-954D-Fight Against Traffic
date: 2019-09-08 22:51:34
categories: Codeforces
tags: ['graph', 'bfs', 'shortest path']
---

## 描述
有一个`nodes`个节点`edges`条边的无向无环图. 每条边权重相同(可以认为都是`1`). 我们关心节点`s`和节点`t`. 现在需要建一条(之前不存在的)边, 问一共有多少种方案. 要求添加新边之后的`s`和`t`之间的最短距离不变.

## 思路
依旧是`floyd`中间点的思想, 不过本题中间点有两个(`i`, `j`), 其中一个可以是`s`或`t`. 分别以`s`和`t`为起点做bfs, 求出`s`点到任意点`i`的最短路数组`ds[i]`, 和`t`点到任意点`i`的最短路数组`dt[i]`.  
对于任意的中间节点`i`和`j`, 只要满足`ds[i] + 1 + dt[j] >= ds[t]`和`ds[j] + 1 + dt[i] >= ds[t]`, 说明经过新修建的边从`s`到`t`的距离并没有变短, `i`和`j`之间允许建一条直接相连的边.

## 代码
```python
import collections
def do():
    nodes, edges, s, t = map(int, input().split(" "))
    s -= 1
    t -= 1
    g = collections.defaultdict(set)
    for _ in range(edges):
        x, y = map(int, input().split(" "))
        x -= 1
        y -= 1
        g[x].add(y)
        g[y].add(x)
    ds = [0] * nodes
    dt = [0] * nodes

    def bfs1(node):
        q = collections.deque()
        q.append((node, 0))
        seen = [0] * nodes
        seen[node] = 1
        while q:
            cur, cost = q.popleft()
            ds[cur] = cost
            for nei in g[cur]:
                if not seen[nei]:
                    seen[nei] = 1
                    q.append((nei, cost + 1))

    def bfs2(node):
        q = collections.deque()
        q.append((node, 0))
        seen = [0] * nodes
        seen[node] = 1
        while q:
            cur, cost = q.popleft()
            dt[cur] = cost
            for nei in g[cur]:
                if not seen[nei]:
                    seen[nei] = 1
                    q.append((nei, cost + 1))
    bfs1(s)
    bfs2(t)
    res = 0
    for i in range(nodes):
        for j in range(i):
            if ds[i] + 1 + dt[j] >= ds[t] and ds[j] + 1 + dt[i] >= ds[t]:
                res += 1
    return res - edges

print(do())
```
