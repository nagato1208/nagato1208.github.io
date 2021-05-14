---
title: codeforces-1187-A-B总结
date: 2019-09-01 21:29:40
categories: Codeforces
tags: ['string', 'math', 'greedy', 'hashmap']
---
2019年8月31日CF群打卡题: 1187A, 1187B.

## 1187A Stickers and Toys
### 描述

数学题. 给`n`个袋子, 这些袋子里一共有`s`个甲物品, `t`个乙物品. 已知每个袋子里的情况只能是以下三种:
- 装一个甲物品
- 装一个乙物品
- 甲乙各装一个.

问至少拿多少个袋子, 保证甲乙物品至少各有一个.

### 代码
水题直接贴代码...
```python
def do():
    N = int(input())
    res = []
    for _ in range(N):
        n, s, t = map(int, input().split(" "))
        res.append(n - min(s, t) + 1)
    for r in res:
        print(r)
do()
``` 

## 1187B Letters Shop
### 描述
给一个字符串`s`, 和一系列的query(都是string), 问每次形成这些query string, 从0开始取`s`的字符, 问至少到哪里(index)可以满足. (`s[0:index+1]`的字符可以表示出query string, 字符不能多次用, 但可以有剩余). 数据范围比较大, 所有query string长度之和会达到`200000`.

### 思路
预处理: 对`s`中出现的每个字符(最多也就`26`个), 都保存有序的下标list. 然后每来一个query, 统计这个query的字符和对应的数目. 用`count`直接可以找到对应字符在`s`中最早的满足条件的下标.

### 代码
```python
import collections
def do():
    length = int(input())
    s = input()
    d = collections.defaultdict(list)
    res = []
    for i, c in enumerate(s):
        d[c].append(i)
    N = int(input())
    for _ in range(N):
        name = input()
        count = collections.Counter(name)
        res.append(max(d[c][count[c]-1] for c in count)+1)
    for r in res:
        print(r)
 
do()
```  