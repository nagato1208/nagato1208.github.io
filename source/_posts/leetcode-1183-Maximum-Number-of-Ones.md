---
title: leetcode-1183-Maximum Number of Ones
date: 2019-09-08 22:04:29
categories: Leetcode
tags: ['greedy', '你真的很弱', 'math']
---

## 描述
(搬运)
Consider a matrix `M` with dimensions `width` * `height`, such that every cell has value `0` or `1`, and any square sub-matrix of `M` of size `sideLength` * `sideLength` has at most `maxOnes` ones.

Return the maximum possible number of ones that the matrix `M` can have.

Example 1:
```ignorelang
Input: width = 3, height = 3, sideLength = 2, maxOnes = 1
Output: 4
Explanation:
In a 3*3 matrix, no 2*2 sub-matrix can have more than 1 one.
The best solution that has 4 ones is:
[1,0,1]
[0,0,0]
[1,0,1]
```

Example 2:
```ignorelang
Input: width = 3, height = 3, sideLength = 2, maxOnes = 2
Output: 6
Explanation:
[1,0,1]
[1,0,1]
[1,0,1]
```
Constraints:
```ignorelang
1 <= width, height <= 100
1 <= sideLength <= width, height
0 <= maxOnes <= sideLength * sideLength
```
## 思路
这个贪心姿势真是想不到... 或者说就算想到, 自己也会立刻否定它的正确性...contest时总是觉得中心对称好像也行:(

暂时当做结论题好了, 要达到全局最优, 每个滑窗必然有重复pattern, 然后我们把`sideLength * sideLength`的滑窗flatten一下, 分别利用大矩形在滑窗内的映射点, 计算每一个位置的贡献, 然后统计前`maxOnes`大的贡献之和.


## 代码
```python
class Solution(object):
    def maximumNumberOfOnes(self, width, height, sideLength, maxOnes):
        """
        :type width: int
        :type height: int
        :type sideLength: int
        :type maxOnes: int
        :rtype: int
        """
        count = [0] * (sideLength * sideLength)
        for w in xrange(width):
            for h in xrange(height):
                index = sideLength * (w % sideLength) + h % sideLength
                count[index] += 1
        return sum(sorted(count, reverse=True)[:maxOnes])
```