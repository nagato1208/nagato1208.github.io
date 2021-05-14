---
title: codeforces-82D-Two out of Three
date: 2019-09-08 22:18:23
categories: Codeforces
tags: ['DP']
---

## 描述
有一个顾客服务系统, 可以每次从队伍的最前面三个人中选两个来同时服务. (剩下的那个人成为后面队伍中的第一个人). 被选中的那两个人的服务时间以更长的那个为准. (服务同时开始, 同时结束). 如何安排服务顺序, 使得总的服务时间最短? 

输入: 服务每个顾客的所需时长`costs`, `costs[i]`表示第`i`个顾客需要被服务的时长.

输出: 最短时间和服务方案. (如果总时间有并列, 优先服务编号小的).

## 思路
和lc-1187很像, 我们用一个`pre`表示上一轮剩下的那个人的编号. dfs+memo. (除结束点以外)每个点有三个分支: 剩下第一(`pre`)个人or第二个人or第三个人. 然后返回时间最小的分支.
本题还要输出方案, 那么用一个`next`字典记录最优的状态`(i, pre)`转移. `memo`数组同时记录最优时间和对应的本轮选择方案. 如果三个分支有并列, 优先选出现早的. 


## 代码
```python
def do():
    n = int(input())
    costs = [int(c) for c in input().split(" ")]
    next = {}
    memo = {}

    def dp(i, pre):
        if i == n:
            memo[i, pre] = [costs[pre], [pre]]
            return memo[i, pre][0]
        if i == n - 1:
            memo[i, pre] = [max(costs[pre], costs[i]), [pre, i]]
            return memo[i, pre][0]
        if (i, pre) not in memo:
            left1 = max(costs[i], costs[i+1]) + dp(i + 2, pre)
            left2 = max(costs[pre], costs[i+1]) + dp(i + 2, i)
            left3 = max(costs[pre], costs[i]) + dp(i + 2, i + 1)
            m = min(left1, left2, left3)
            if m == left3:
                cur = [pre, i]
                next[i, pre] = (i + 2, i + 1)
            elif m == left2:
                cur = [pre, i + 1]
                next[i, pre] = (i + 2, i)
            else:
                cur = [i, i + 1]
                next[i, pre] = (i + 2, pre)
            memo[i, pre] = [m, cur]
        return memo[i, pre][0]

    print(dp(1, 0))
    state = (1, 0)
    # print(next)
    while True:
        print(" ".join(str(c+1) for c in memo[state][1]))
        if state not in next:
            break
        state = next[state]
    return 0

do()
```


