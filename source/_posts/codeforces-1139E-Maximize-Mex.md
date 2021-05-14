---
title: codeforces-1139E-Maximize Mex[二分图]
date: 2019-09-13 00:52:38
categories: Codeforces
tags: ['hungarian', 'graph', 'math', '离线算法']
---

## 描述
(复制粘贴的) 在一所学校中有 n 个学生和 m 个俱乐部。俱乐部从 1 到 m 标号。每个学生拥有一个潜力值 pi，且属于第 ci 个俱乐部。

刚开始每个学生都恰好属于一个俱乐部，后来学校举办了一次技能测试，这场测试持续了 d 天。每天上午都会有恰好一个学生离开他的俱乐部。一个学生一旦离开就不会再次加入任何一个俱乐部。每天下午，每个俱乐部需要选出一个人，以此组成一个 m 个人的队伍来参加比赛。一支队伍的能力为队员潜力值的 mex（mex指在一个序列中没有出现的最小自然数）。学校想知道每一天能组成的队伍的能力最大值。

## 思路
(并不是很容易想到的)匹配问题. 首先query是基于删除边的, 删除操作不好处理, 离线一下改成添加边. 然后对于潜力值->俱乐部的二分图(映射的中间变量学生可以被忽略), 找到(从`0`开始的)最大的匹配值`mex`. 注意因为我们在不断添加边, 等于是增加候选边, 从后向前的`mex`值是**非递减**的. 所以每次匈牙利算法的起点都是上一轮的`mex`值. 初始为`0`. 

总之实际在完成的任务是**为从0开始的潜力值依次分配俱乐部, 找到尽可能多的俱乐部来匹配潜力值**, 潜力值不能再增加(没有俱乐部可以提供/匹配这个潜力值)时, 就是本轮的结果. 

## 代码

```python
# 1139E
import collections
def do():
    students, clubs = map(int, input().split(" "))
    p = [int(i) for i in input().split(" ")]
    stu2club = [int(i) - 1 for i in input().split(" ")]
    g = collections.defaultdict(set)
    days = int(input())
    delete = [False] * students
    query = [0] * days
    res = [0] * (days + 1)
    match = [-1] * clubs
    for _ in range(days):
        i = int(input()) - 1
        delete[i] = True
        query[_] = i
    for i in range(students):
        if not delete[i]:
            g[p[i]].add(stu2club[i]) # p -> club

    def dfs(mex):
        for club in g[mex]:
            if not seen[club]:
                seen[club] = 1
                if match[club] == -1 or dfs(match[club]):
                    match[club] = mex
                    return True
        return False

    for i in range(days - 1, -1, -1):
        res[i] = res[i + 1]
        seen = [0] * clubs
        while dfs(res[i]):
            res[i] += 1
            seen = [0] * clubs
        new_student = query[i]
        g[p[new_student]].add(stu2club[new_student])
        
    for i in range(days):
        print(res[i])

do()
```
