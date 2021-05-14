---
title: leetcode-1199. Minimum Time to Build Blocks
date: 2019-09-21 17:51:03
categories: Leetcode
tags: ['greedy', 'binary search', 'heap']
---
## 描述
(搬运)
You are given a list of blocks, where `blocks[i] = t` means that the `i`-th block needs `t` units of time to be built. A block can only be built by exactly one worker.

A worker can either split into two workers (number of workers increases by one) or build a block then go home. Both decisions cost some time.

The time cost of spliting one worker into two workers is given as an integer `split`. Note that if two workers split at the same time, they split in parallel so the cost would be `split`.

Output the minimum time needed to build all blocks.

Initially, there is only **one** worker.

Example 1:
```ignorelang
Input: blocks = [1], split = 1
Output: 1
Explanation: We use 1 worker to build 1 block in 1 time unit.
```

Example 2:
```ignorelang
Input: blocks = [1,2], split = 5
Output: 7
Explanation: We split the worker into 2 workers in 5 time units then assign each of them to a block so the cost is 5 + max(1, 2) = 7.
```

Example 3:
```ignorelang
Input: blocks = [1,2,3], split = 1
Output: 4
Explanation: Split 1 worker into 2, then assign the first worker to the last block and split the second worker into 2.
Then, use the two unassigned workers to build the first two blocks.
The cost is 1 + max(3, 1 + max(1, 2)) = 4.
```
Constraints:
```ignorelang
1 <= blocks.length <= 1000
1 <= blocks[i] <= 10^5
1 <= split <= 100
```

## 思路
- 二分模板题. 枚举时间限制, 用一个`check/can`函数判断任务是否在这种限制内可以完成.
- 利用`haffman编码`的思路. 贪心原则是**用一个(分裂)出现更早的工人, 去完成代价更大的任务**, 这样永远比反过来所需时间要少.


## 代码
二分:
```python
class Solution(object):
    def minBuildTime(self, blocks, split):
        """
        :type blocks: List[int]
        :type split: int
        :rtype: int
        """
        
        def can(limit):
            n = len(blocks)
            i = 0
            usable = 1 #初始的一个工人
            cur = 0
            while i < n and 0 < usable <= n - i:
                while i < n and cur + blocks[i] + split > limit and usable > 0:
                    if cur + blocks[i] > limit: # 额外检查一下, 这里是否可以使用这个工人
                        return False
                    i += 1
                    usable -= 1
                cur += split
                usable <<= 1
            if i < n and cur + blocks[i] > limit:
                return False
            return usable >= n - i
        
        
        blocks.sort(reverse=True)
        lo, hi = 0, 9999999999
        while lo < hi:
            mi = (lo + hi) >> 1
            t = can(mi)
            if t:
                hi = mi
            else:
                lo = mi + 1
        return lo
```


贪心:
```python
class Solution(object):
    def minBuildTime(self, blocks, split):
        """
        :type blocks: List[int]
        :type split: int
        :rtype: int
        """
        heapq.heapify(blocks)
        while len(blocks) >= 2:
            x = heapq.heappop(blocks)
            y = heapq.heappop(blocks)
            heapq.heappush(blocks, y + split)
        return blocks[0]
```