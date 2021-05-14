---
title: hackerrank-Maximum Xor
date: 2019-09-06 00:58:25
categories: Hackerrank
tags: ['string', 'trie', 'bit']
---

## 描述
原题在这: https://www.hackerrank.com/challenges/maximum-xor/problem
给一个数组, 和一系列query, 对每个query求出数组元素和它`xor`的最大值. 数组长度和query长度都是最大`100000`.

## 思路
brute force是`O(n**2)`肯定不行. 不过最优解并不难想到, 模拟手算就行. 每来一个query, (从高位到低位)我们优先找和它这一位值相反的数, 这样`xor`才最大. 用trie实现.

## 代码
```python
def maxXor(arr, queries):
    # arr: [1, 3, 5, 7]
    # queries: [17, 6, ...]
    trie = {}
    for num in arr:
        cur = trie
        for i in range(31, -1, -1):
            b = (num >> i) & 1 # 这一位是不是1
            if b not in cur:
                cur[b] = {}
            cur = cur[b]
        cur["#"] = 1
    res = []
    for q in queries:
        p = 0  # p是arr中的最佳候选, 来和q生成xor最大值
        cur = trie
        for i in range(31, -1, -1):
            b = 1 ^ ((q >> i) & 1)
            if b not in cur:
                cur = cur[1 ^ b]
                p |= ((1 ^ b) << i)
            else:
                cur = cur[b]
                p |= (b << i)
        res.append(q ^ p)
    return res
```