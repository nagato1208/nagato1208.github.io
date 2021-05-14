---
title: codeforces-936B-Sleepy Game[图上博弈]
date: 2019-09-12 00:39:55
categories: Codeforces
tags: ['dp', 'tree', 'graph', 'game', 'stack']
---

## 描述
给一个任意图, 一个先手的起始点. 两个人按顺序走子, 第一个不能再前进的人输. 具体描述去官网看. 因为可能有环, (预料到自己要输的那个人)可以无限次在环上移动, 这个时候判平局`Draw`. 
判断先手的结果(`Win`, `Draw`, `Lose`).

## 思路
- 先手胜利的条件是, 从起点开始**可以走奇数步到达一个出度为`0`的点**. (这时轮到后手走, 但是她已经不能再动了).
- 如果存在奇数长度的环, 走一遍环, 步数的奇偶性也会改变
- 因为上一点, 访问过的节点还可以再访问一遍, 当且仅当到达这个节点的步数奇偶性不同. 所以要开一个`2*nodes`的`dp`或者`seen`二维数组. `dp[i][0]`表示是否偶数步走到`i`点, `dp[i][1]`表示是否在奇数步走到`i`点.
- 开一个`pre`数组来记录上一个值(节点转移)
- 在dfs的过程中顺便判断是否有环. 有环的话, 先手肯定不会输. 
- dfs递归版本的代码正确但是Python果断TLE, 于是改成stack, WA了几发之后ac. 为什么会WA呢? 因为没注意到后续遍历...判断环时用到了mask数组表示这个点是否正在被访问, 类似`mask[i]=1;dfs(i);mask[i]=0`的操作, 只有在`i`这个节点的所有子节点都被访问完之后, `mask[i]`才能恢复成`0`. 属于后序遍历. 用stack的话, 需要记录第一次访问, 第二次访问. 只有第二次访问时才可以彻底出栈, 设置`mask[i]=0`.

## 代码
AC版本, stack:
```python
# 936B
import collections

def do():
    nodes, edges = map(int, input().split(" "))
    outd = [0] * (nodes + 1)
    g = collections.defaultdict(list)
    for i in range(1, nodes + 1):
        tmp = [int(c) for c in input().split(" ")]
        outd[i] = tmp[0]
        g[i] = tmp[1:]
    dp = [[0] * 2 for _ in range(nodes + 1)]
    pre = [[0] * 2 for _ in range(nodes + 1)]
    st = int(input())
    mask = [0] * (nodes + 1)

    def dfs(entry):
        has_loop = False
        stack = [[entry, 0, True]]
        dp[entry][0] = 1 # d[st][0], 0 means reach here by even(0) steps
        while stack:
            cur, step, first = stack.pop()
            if first:
                mask[cur] = 1
                stack.append([cur, -1, False])
                for nei in g[cur]:
                    if mask[nei]:
                        has_loop = True
                    if dp[nei][step ^ 1]:
                        continue
                    pre[nei][step ^ 1] = cur
                    dp[nei][step ^ 1] = 1
                    stack.append([nei, step ^ 1, first])
            else:
                mask[cur] = 0
        return has_loop

    has_loop = dfs(st)
    for i in range(1, nodes + 1):
        if outd[i] == 0: # out degree
            if dp[i][1]: # must reach here by odd steps
                print("Win")
                res = []
                cur = i
                step = 1
                while cur != st or step:
                    res.append(cur)
                    cur = pre[cur][step]
                    step ^= 1
                res.append(st)
                print(" ".join(str(c) for c in res[::-1]))
                return
    res = "Draw" if has_loop else "Lose"
    print(res)

do()
```

TLE, recursive dfs:
```python
# 936B
import collections

def do():
    nodes, edges = map(int, input().split(" "))
    outd = [0] * (nodes + 1)
    g = collections.defaultdict(list)
    for i in range(1, nodes + 1):
        tmp = [int(c) for c in input().split(" ")]
        outd[i] = tmp[0]
        g[i] = tmp[1:]
    dp = [[0] * 2 for _ in range(nodes + 1)]
    pre = [[0] * 2 for _ in range(nodes + 1)]
    st = int(input())
    mask = [0] * (nodes + 1)
    has_loop = False

    def dfs(cur, step):
        nonlocal has_loop
        for nei in g[cur]:
            if mask[nei]:
                has_loop = True
            if dp[nei][step ^ 1]:
                continue
            pre[nei][step ^ 1] = cur
            dp[nei][step ^ 1] = 1
            mask[nei] = 1
            dfs(nei, step ^ 1)
            mask[nei] = 0

    dp[st][0] = 1 # d[st][0], 0 means reach here by even(0) steps
    mask[st] = 1
    dfs(st, 0)

    for i in range(1, nodes + 1):
        if outd[i] == 0: # out degree
            if dp[i][1]: # must reach here by odd steps
                print("Win")
                res = []
                cur = i
                step = 1
                while cur != st or step:
                    res.append(cur)
                    cur = pre[cur][step]
                    step ^= 1
                res.append(st)
                print(" ".join(str(c) for c in res[::-1]))
                return 0
    if has_loop:
        print("Draw")
    else:
        print("Lose")
    return 0

do()
```