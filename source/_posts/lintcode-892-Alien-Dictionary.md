---
title: lintcode-892 Alien Dictionary
date: 2019-09-26 17:13:01
categories: Lintcode
tags: ['bfs', 'dfs', 'topological sort']
---

## 描述
There is a new alien language which uses the latin alphabet. However, the order among letters are unknown to you. You receive a list of non-empty words from the dictionary, where words are sorted lexicographically by the rules of this new language. Derive the order of letters in this language.
```ignorelang
You may assume all letters are in lowercase.
You may assume that if a is a prefix of b, then a must appear before b in the given dictionary.
If the order is invalid, return an empty string.
There may be multiple valid order of letters, return **the smallest in normal lexicographical order**
```

Example:
```ignorelang
Input：["wrt","wrf","er","ett","rftt"]
Output："wertf"
Explanation：
from "wrt"and"wrf" ,we can get 't'<'f'
from "wrt"and"er" ,we can get 'w'<'e'
from "er"and"ett" ,we can get 'r'<'t'
from "ett"and"rtff" ,we can get 'e'<'r'
So return "wertf"
```

## 思路
典型拓扑排序. 因为返回可能的结果中字典序最小的, 在建立`res`数组时, 队列的pop顺序要从小到大. 所以队列用pq(heap).

## 代码
```python
import heapq
class Solution:
    """
    @param words: a list of words
    @return: a string which is correct order
    """
    def alienOrder(self, words):
        # Write your code here
        g = collections.defaultdict(set)
        ind = collections.defaultdict(int)
        
        for i in xrange(1, len(words)):
            for j in xrange(min(len(words[i-1]), len(words[i]))):
                if words[i-1][j] != words[i][j]:
                    g[words[i-1][j]].add(words[i][j])
                    ind[words[i][j]] += 1
                    break
        hp = []
        res = []
        char_set = set("".join(w for w in words))
        for c in char_set:
            if ind[c] == 0:
                heapq.heappush(hp, c)
        
        while hp:
            cur = heapq.heappop(hp)
            res.append(cur)
            for nei in g[cur]:
                ind[nei] -= 1
                if ind[nei] == 0:
                    heapq.heappush(hp, nei)
                    
        return "".join(res) if len(res) == len(char_set) else ""
```
