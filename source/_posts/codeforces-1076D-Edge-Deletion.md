---
title: codeforces-1076D-Edge Deletion
date: 2019-09-06 18:38:00
categories: Codeforces
tags: ['shortest path', 'graph', 'dijkstra']
---

## 描述
给定一个`n`个点，`m`条边, 用邻接表表示的无向连通图. 每个点`i`都和点`1`有一个最短距离.

现在需要删去一些边，让剩余的边数最多为`k`，并且让尽量多的到节点`1`的最短距离保持不变.

给定的边编号`1~m`，找到最佳删边方案，打印保留的边的编号.

## 思路
`dijkstra`长时间不写还给WA了几发... 直接从起点`1`开始遍历最短路, 把找到的前`min(n-1, k)`条unique的边加入结果.

## 代码
```python
import collections
import heapq
def do():
    nodes, edges, k = map(int, input().split(" "))
    graph = collections.defaultdict(dict)
    es = {}
    for i in range(edges):
        x, y, w = map(int, input().split(" "))
        graph[x][y] = w
        graph[y][x] = w
        es[(x, y)] = es[(y, x)] = i + 1
 
    seen = set()
    seen.add(1)
    hp = []
    for nei in graph[1]:
        heapq.heappush(hp, (graph[1][nei], nei, es[(1, nei)]))
 
    rl = min(k, nodes - 1)
    res = set()
    while len(res) < rl:
        cost, cur, edge = heapq.heappop(hp)
        if cur in seen:   # 有一次WA是因为这里忘了check了...
            continue
        seen.add(cur)
        res.add(edge)
        for nei in graph[cur]:
            if nei not in seen:
                heapq.heappush(hp, (cost + graph[cur][nei], nei, es[(cur, nei)]))
 
    print(rl)
    print(" ".join(str(n) for n in list(res)))
    return 0
 
do()
```