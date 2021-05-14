---
title: leetcode-1246-Palindrome Removal
date: 2019-11-02 11:09:29
categories: Leetcode
tags: ['dp']
---

## 描述
(搬运)
Given an integer array `arr`, in one move you can select a **palindromic** subarray `arr[i], arr[i+1], ..., arr[j]` where i <= j, and remove that subarray from the given array. Note that after removing a subarray, the elements on the left and on the right of that subarray move to fill the gap left by the removal.

Return the minimum number of moves needed to remove all numbers from the array.

Examples:
```ignorelang
Input: arr = [1,2]
Output: 2
```
```ignorelang
Input: arr = [1,3,4,1,5]
Output: 3
Explanation: Remove [4] then remove [1,3,1] then remove [5].
```
Constraints
```ignorelang
1 <= arr.length <= 100
1 <= arr[i] <= 20
```

## 思路
性质题, 对于每一个数字都有两种选择: 1) 选一个和它一样的数字消去, 如果这两个数字的位置之间有其他数字, 当前消去的过程不增加remove的次数 2) 直接消去它自己, remove的次数+1. 
区间DP或者DFS+记忆

DP pattern: 
```python
dp[i][j] = min(dp[i][j], dp[i][k-1] + dp[k+1][j]), where arr[k]==arr[i]
```


## 代码
DFS
```python
class Solution(object):
    def minimumMoves(self, arr):
        """
        :type arr: List[int]
        :rtype: int
        """
        memo = {}
        d = collections.defaultdict(list)
        for i,c in enumerate(arr):
            d[c].append(i)
        
        def dp(i,j):
            if i == j:
                return 1
            if i > j:
                return 0
            if (i,j) not in memo:
                res = 1 + dp(i+1, j)
                for k in d[arr[i]]:
                    if k == i + 1:
                        res = min(res, 1 + dp(k+1, j))
                    elif i + 1 < k <= j:
                        res = min(res, dp(i+1, k-1) + dp(k+1, j))
                memo[i,j] = res
            return memo[i,j]
        
        return dp(0, len(arr)-1)
```

Bottom-up DP:
```python
class Solution(object):
    def minimumMoves(self, arr):
        """
        :type arr: List[int]
        :rtype: int
        """
        n = len(arr)
        dp = [[100] * n for _ in range(n)]
                
        for i in range(n-1, -1, -1):
            dp[i][i] = 1
            for j in range(i+1, n):
                if j == n:
                    break
                dp[i][j] = 1 + (dp[i+1][j] if i+1<n else 0)
                for k in range(i+1, j+1):
                    if arr[i] == arr[k]:
                        if k == i + 1:
                            dp[i][j] = min(dp[i][j], 1 + (dp[k+1][j] if k+1<=j else 0))
                        else:
                            dp[i][j] = min(dp[i][j], dp[i+1][k-1] + (dp[k+1][j] if k+1<=j else 0))
        return dp[0][n-1]
```

