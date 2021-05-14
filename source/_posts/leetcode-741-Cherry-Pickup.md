---
title: leetcode-741-Cherry-Pickup
date: 2019-09-12 14:03:01
categories: Leetcode
tags: ['DP']
---

## 描述
一张边长为`n`的二维矩阵, `0`表示可走, `-1`表示障碍物(不可走), `1`表示樱桃(可走, 走过之后变为`0`因为被摘掉了). 从`0,0`往返`n-1,n-1`的路上, 最多摘多少樱桃.

## 思路
其实可以看做两个人同时从`0,0`出发, 同时到达`n-1, n-1`, 最多摘到多少樱桃. 如果两个人位置相同并且当前是樱桃, 那么只摘一次. 开一个三维`dp`数组, `dp[r1][c1][c2]`表示甲从`r1, c1`出发, 乙从`r2, c2`出发, 最多摘的数量. 因为到达当前状态的步数是相同的, 所以`r1+c1 == r2+c2`, `r2`的值可以用前三个变量表示出来.


## 代码
```python
class Solution(object):
    def cherryPickup(self, grid):
        """
        :type grid: List[List[int]]
        :rtype: int
        """
        N = len(grid)
        memo = [[[-1] * N for _ in xrange(N)] for __ in xrange(N)]
        
        def dp(r1, c1, c2):
            r2 = r1 + c1 - c2
            if (N == r1 or N == r2 or N == c1 or N == c2 or
                    grid[r1][c1] == -1 or grid[r2][c2] == -1):
                return float('-inf')
            if r1 == c1 == N-1:
                return grid[r1][c1]
            if memo[r1][c1][c2] == -1:
                res = grid[r1][c1] + (c1 != c2) * grid[r2][c2]
                res += max(dp(r1, c1+1, c2+1), dp(r1+1, c1, c2+1),
                           dp(r1, c1+1, c2), dp(r1+1, c1, c2))

                memo[r1][c1][c2] = res
            return memo[r1][c1][c2]

        return max(0, dp(0, 0, 0))
```
