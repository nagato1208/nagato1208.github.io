---
title: leetcode-1168 Optimize Water Distribution in a Village
date: 2019-08-25 01:47:14
categories: Leetcode
tags: ['Graph', 'MST']
keywords: ['Graph']
---
## 描述
There are `n` houses in a village. We want to supply water for all the houses by building wells and laying pipes.

For each house `i`, we can either build a well inside it directly with cost `wells[i]`, or pipe in water from another well to it. The costs to lay pipes between houses are given by the array `pipes`, where each `pipes[i] = [house1, house2, cost]` represents the cost to connect `house1` and `house2` together using a pipe. Connections are bidirectional.

Find the minimum total cost to supply water to all houses.

## 思路
每次我们需要一个`well`时(修建在某个`house`上), 它带来的`cost`在数值相当于这个`house`连接到一个`well`的`cost`, 那么其实我们可以只用一个总的`well`(~~中央空调?~~), 每个house在自己的位置修建`well`, 都相当于连接唯一的中央`well`到当前点. 
所以分为两步:
- 在邻接表加入`well`和`house`和`cost`组成的虚拟edge.
- 常规MST. (比赛时用的Prim, 还可以用Kruskal) 

时间复杂度: `O(elog2v) `

## 代码
```python
class Solution(object):
    def minCostToSupplyWater(self, n, wells, pipes):
        """
        :type n: int
        :type wells: List[int]
        :type pipes: List[List[int]]
        :rtype: int
        """
        d = collections.defaultdict(list)
        for i,w in enumerate(wells):
            pipes.append([0, i+1, w])
        for s,e,w in pipes:
            d[s].append((w,s,e))
            d[e].append((w,e,s))
        seen = set()
        seen.add(0)
        usable = d[0][:]
        heapq.heapify(usable)
        res = 0
        while usable and len(seen) < 1 + n:
            w,s,e = heapq.heappop(usable)
            if e not in seen:
                seen.add(e)
                res += w
                for nei in d[e]:
                    if nei[2] not in seen:
                        heapq.heappush(usable, nei)
        return res
```
