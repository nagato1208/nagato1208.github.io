---
title: 'leetcode-1192-Critical Connections in a Network[tarjan求割边]'
date: 2019-09-15 17:26:18
categories: Leetcode
tags: ['graph', 'tarjan', 'dfs']
---

## 描述
给一个无向连通图, 求所有割边. (割边: 去掉这条边, 联通分量就会变成多个)

## 思路
`tarjan`算法模板题. `dfn`数组表示正常dfs的遍历顺序(存的值可以看做时间戳), `low`数组表示该点**不通过父节点**能访问到的最小(时间戳)祖先节点. `low[v] > dnf[u]` 时, 说明`v-u`是割边.(可以这么想, 我们从`v`走到`u`, `v`之前有很多时间戳比`v`小的祖先节点, 现在从`u`出发不经过`v`, 到达的最小时间戳也比`u`大, 说明(断开当前边的话)够不到之前的节点, 联通分量++)

这个博客讲的很好, 懒得复述一遍了: https://www.cnblogs.com/nullzx/p/7968110.html

关于`tarjan`找有向图强连通分量: https://www.cnblogs.com/tgycoder/p/5048898.html 

**当`dfn(u)=Low(u)`时，以`u`为根的搜索子树上所有节点是一个强连通分量**. 结论化: 在一个强连通分量中有且仅有一个点的`dfn`与`low`相等. (她是这个强连通分量的入口节点). 

求有向图强连通分量的意义:  由于强连通分量内部的节点性质相同,可以将一个强连通分量内的节点缩成一个点, 即消除了环, 这样, 原图就变成了一个有向无环图(directed acyclic graph,DAG).
`tarjan`求割边模板:
```python
def tarjan(u):
    low[u] = self.time
    dfn[u] = self.time
    self.time += 1
    for v in g[u]:
        if not dfn[v]:
            parent[v] = u
            tarjan(v)
            low[u] = min(low[u], low[v])
        elif v != parent[u]:
            low[u] = min(low[u], dfn[v])
```
`tarjan`求有向图所有强连通分量模板:
```python
inStack = [0] * (1 + n)
stack = []
def tarjan(u):
    low[u] = self.time
    dfn[u] = self.time
    self.time += 1
    stack.append(u)
    inStack[u] = 1
    for v in g[u]:
        if not dfn[v]:
            tarjan(v)
            low[u] = min(low[u], low[v])  
        elif inStack[v]: 
            low[u] = min(low[u], dfn[v])
    if dfn[u] == low[u]:
        res.append([])
        while True:
            v = stack.pop()
            inStack[v] = 0
            res[-1].append(v)    # 这是我们的结果
            if v == u:
                break
```

## 代码
求割边:
```python
class Solution(object):
    def criticalConnections(self, n, cons):
        """
        :type n: int
        :type connections: List[List[int]]
        :rtype: List[List[int]]
        """
        g = collections.defaultdict(list)
        parent = [-1] * (1 + n)
        low = [float('inf')] * (1 + n)
        dfn = [0] * (1 + n)        
        self.time = 1
        res = []
        
        def tarjan(u):
            low[u] = self.time
            dfn[u] = self.time
            self.time += 1
            for v in g[u]:
                if not dfn[v]:
                    parent[v] = u
                    tarjan(v)
                    low[u] = min(low[u], low[v])  # 没访问过的肯定不是父节点, 不需要check
                    if low[v] > dfn[u]:
                        res.append([u, v]) # low[v] > dnf[u] 时, 说明v-u是桥/割边
                elif v != parent[u]: # 需要check
                    low[u] = min(low[u], dfn[v])

        for x, y in cons:
            g[x].append(y)
            g[y].append(x)
        
        for i in xrange(1, n + 1):
            if not dfn[i]:
                tarjan(i)
        return res
```

求强连通分量(套的本题框架)
```python
class Solution(object):
    def criticalConnections(self, n, cons):
        """
        :type n: int
        :type connections: List[List[int]]
        :rtype: List[List[int]]
        """
        g = collections.defaultdict(list)
        parent = [-1] * (1 + n)
        low = [float('inf')] * (1 + n)
        dfn = [0] * (1 + n)        
        self.time = 1
        res = []
        
        inStack = [0] * (1 + n)
        stack = []
        def tarjan(u):
            low[u] = self.time
            dfn[u] = self.time
            self.time += 1
            stack.append(u)
            inStack[u] = 1
            for v in g[u]:
                if not dfn[v]:
                    tarjan(v)
                    low[u] = min(low[u], low[v])  
                elif inStack[v]: 
                    low[u] = min(low[u], dfn[v])
            if dfn[u] == low[u]:
                res.append([])
                while True:
                    v = stack.pop()
                    inStack[v] = 0
                    res[-1].append(v)
                    if v == u:
                        break

        for x, y in cons:
            g[x].append(y)   # 是有向图
        
        for i in xrange(1, n + 1):
            if not dfn[i]:
                tarjan(i)
        return res
```