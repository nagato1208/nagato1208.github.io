---
title: 'codeforces-1204C-Anna, Svyatoslav and Maps题解[压缩路径]'
date: 2019-09-04 14:05:55
categories: Codeforces
tags: ['shortest path', 'graph', 'floyd']
---

## 描述
给一张邻接矩阵表示有向图, 和一条路径. 压缩这条路径(删除尽可能多的点), 使得压缩后的路径没有歧义. (本着找最短路径走的思想, 不会走出和原来不一样的路径)

## 思路
先计算一遍多源最短路. 然后从起点开始走, 用一个变量`cur`表示当前走过的代价. 

比如目前正在处理`x`点和`y`点: 对于已经放到结果中的最后一个点`res[-1]`, 如果`cur + dis[x][y]`的值大于`dis[res[-1]][y]`, 说明从`res`最后一个点`res[-1]`到`y`点, 存在不经过`x`点的最短路. 这个时候, 如果忽略`x`, 就会出现错误的解压缩路径(去走不经过`x`的更短的路径了), 所以必须把`x`放进结果中. 然后`x`成为了新的`res[-1]`, 以此类推. 最后把终点放入结果.

换句话说, 一个点在什么情况下可以被删除? **如果前面一个保留的点`res[-1]`到这个点(`x`)后一个点(`y`)的最短路径等于前面一个点经过这个点到这个点后面一个点的最短路径(太绕了, 其实是`res[-1] => x => y`)相等,那么这个点`x`可以被删除**.

## 代码

```python
def do():
    n = int(input())
    graph = [[0] * n for _ in range(n)]
    for i in range(n):
        tmp = [int(c) for c in input()]
        for j in range(len(tmp)):
            graph[i][j] = 1 if tmp[j] == 1 else float('inf')
    n_path = int(input())
    path = [int(c) - 1 for c in input().split(" ")]
    dis = [[float('inf')] * n for _ in range(n)]
    for i in range(n):
        for j in range(n):
            if i == j:
                dis[i][i] = 0
            else:
                dis[i][j] = graph[i][j]
    for k in range(n):
        for i in range(n):
            if i != k:
                for j in range(n):
                    dis[i][j] = min(dis[i][j], dis[i][k] + dis[k][j])
    res = [path[0]]
    cur = 0
    for i in range(1, n_path):
        cur += dis[path[i-1]][path[i]]
        if cur > dis[res[-1]][path[i]]:
            res.append(path[i-1])  # 目前路径中的最后一个点是path[i-1]
            cur = dis[res[-1]][path[i]] # 目前代价中的最后一个点是path[i]
    if path[-1] != res[-1]:
        res.append(path[-1])
    print(len(res))
    print(" ".join([str(c+1) for c in res]))

do()
```