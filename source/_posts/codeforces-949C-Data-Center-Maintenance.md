---
title: codeforces-949C-Data Center Maintenance[最小强连通分量]
date: 2019-09-12 16:41:44
categories: Codeforces
tags: ['graph', 'dfs']
---

## 描述
有n个数据中心, m个客户, 一天有h小时, 每个数据中心在每天的一个特定小时宕机, 每个用户的数据保存在两个数据中心上, 数据保证每个用户在每个小时都可以访问到数据, 也就是不存在一个用户保存数据的两个中心宕机时间相同. 现要选出一部分中心将他们的宕机时间向后推迟1h, 且还要保证用户对数据的随时访问, 问最少要选几个机器(不能选0个).

## 思路
对于同一个客户的两台机器, 如果宕机时间(其实原文是维护时间...)间隔是1, 那么前一个机器推迟, 后一个必然也要推迟.(另外考虑24h-1h)这种情况, 需要取模.

对于`机器A推迟, 机器B也要推迟`的情况, 我们在A->B之间建边. 这样形成一张有向图, 原题转化成了求**最小强连通分量**(每一个连通块推迟了一个点，则整个块都需要被推迟).
另外对于size为`1`的情况, 特判一下出度是否为`0`.

关于`kosaraju`算法求强连通分量: https://oi-wiki.org/graph/scc/#kosaraju

## 代码
```python
# 949C
import collections
def do():
    nodes, clients, h = map(int, input().split(" "))
    times = [int(t) for t in input().split(" ")]
    g = collections.defaultdict(list)
    g2 = collections.defaultdict(list) # 反向图
    for _ in range(clients):
        x, y = map(int, input().split(" "))
        tx = times[x - 1]
        ty = times[y - 1]
        if (tx + 1) % h == ty:
            g[x].append(y)
            g2[y].append(x)
        if (ty + 1) % h == tx:
            g[y].append(x)
            g2[x].append(y)
    seen = [0] * (1 + nodes)
    colors = [0] * (1 + nodes)
    post_stack = []
    
    def dfs1(entry):
        seen[entry] = 1
        stack = [[entry, True]]
        while stack:
            cur, first = stack.pop()
            if first:
                stack.append([cur, not first])
                for nei in g[cur]:
                    if not seen[nei]:
                        seen[nei] = 1
                        stack.append([nei, True])
            else:
                post_stack.append(cur)

    def dfs2(entry, color):
        stack = [entry]
        colors[entry] = color
        while stack:
            cur = stack.pop()
            for nei in g2[cur]:
                if not colors[nei]:
                    colors[nei] = color
                    stack.append(nei)

    for i in range(1, nodes + 1):
        if not seen[i]:
            dfs1(i)
    cur_color = 1
    while post_stack:
        i = post_stack.pop()
        if not colors[i]:
            dfs2(i, cur_color)
            cur_color += 1
            
    d = collections.defaultdict(list)
    for i, c in enumerate(colors):
        d[c].append(i)
    d.pop(0)
    for can in sorted(d.keys(), key=lambda x:len(d[x])):
        if len(d[can]) == 1:
            if len(g[d[can][0]]) == 0:
                print(1)
                print(d[can][0])
                return 0
        else:
            print(len(d[can]))
            print(" ".join([str(c) for c in d[can]]))
            return 0
    return 0

do()

```
