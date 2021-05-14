---
title: codeforces-295B-Greg and Graph
date: 2019-09-06 01:14:58
categories: Codeforces
tags: ['floyd', 'graph', 'shortest path', 'dp']
---

## 描述
对于一个邻接矩阵表示的有向完全图, 我们有一个节点删除序列(最终会删完所有节点). 以这个序列的顺序进行删除, 求出在每次删除之前, 所有(连通的)点对的最短路之和. 
节点数量最多`500`个.

## 思路
需要深入理解`floyd`算法... 最外层的`k`的作用是, 用来标定以`k`为中间节点, `i`和`j`的最短路是什么. 那么对于删掉的节点, 已经不存在了, 最短路不能再经过它. 
这样我们可以逆序进行一次多源最短路搜索.
- 倒数第一个节点(删除之前), 此时只有一个节点, 最短路之和为`0`
- 倒数第二个节点, 此时只有两个节点, 最短路直接就是他们的一来一回的距离之和.
- 倒数第三个节点, 此时每个节点都可以作为另外两个节点最短路的中间节点. `floyd`应用. 依次类推.
- 每次更新完**只允许k个节点作为中间节点**的最短路, 都求一次**这k个节点两两之间**最短路之和, 最后逆序输出.(负负得正)
- 需要注意的是, 因为删除节点的标号是无序的, 但是`floyd`算法的模板(下标)是有序的, 需要额外把各个节点hash一下, 赋一个id.

## 代码
```python
def do():
    n = int(input())
    graph = [[0] * n for _ in range(n)]
    dp = [[0] * n for _ in range(n)]
    d = {}
    for i in range(n):
        for j, c in enumerate(input().split(" ")):
            graph[i][j] = int(c)
    nodes = [int(c) for c in input().split(" ")]
    for i, node in enumerate(nodes):
        d[node - 1] = n - i - 1
    for i in range(n):
        for j in range(n):
            dp[d[i]][d[j]] = graph[i][j]
    res = [0] * n
    for k in range(n):
        for i in range(n):
            for j in range(n):
                dp[i][j] = min(dp[i][j], dp[i][k] + dp[k][j])
        for i in range(k+1):
            for j in range(k+1):
                res[k] += dp[i][j]
    return " ".join(str(c) for c in res[::-1])
 
print(do())
```


