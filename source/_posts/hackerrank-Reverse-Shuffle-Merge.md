---
title: hackerrank-Reverse Shuffle Merge
date: 2019-09-05 00:07:16
categories: Hackerrank
tags: ['string', 'stack', 'greedy']
---

## 描述
原题在这: https://www.hackerrank.com/challenges/reverse-shuffle-merge/problem
大意是定义了几个操作, `reverse`, `permutation(shuffle)`, 然后给你`merge(reverse(s), permutation(s))`的结果, 问可能的最小字典序的原始字符串是什么.

## 思路
标准单调栈. 逆序处理(因为有reverse就再reverse一下, 反正permutation不受逆序影响). 当前值比栈顶小的时候, 不能直接pop, 还需要满足
- 对于栈顶字符`res[-1]`, 剩余未处理的字符串中`res[-1]`的数量 + 栈中`res[-1]`数量减`1`(即将pop出) 需要大于总数量的一半. (这样`res[-1]`才够用, 来形成结果)
- 既然`res[-1]`是要被当前字符`c`替换掉的, 目前栈中的`c`数量必须小于总数量一半, 不可以超过.

## 代码
```python
import collections

s = input()
s = s[::-1]
count = collections.Counter(s)
res = []
left = dict(count)
cur = collections.defaultdict(int)
for c in s:
    while res and res[-1] > c and cur[res[-1]] - 1 + left[res[-1]] >= count[res[-1]]//2 and cur[c] < count[c]//2:
        cur[res[-1]] -= 1
        res.pop()
    if cur[c] < count[c]//2:
        res.append(c)
        cur[c] += 1
    left[c] -= 1

print("".join(res))
```