---
title: 'leetcode-1187-Make Array Strictly Increasing-[DP]'
date: 2019-09-08 18:24:00
categories: Leetcode
tags: ['DP', '你真的很弱', 'dfs']
---

## 描述
给两个数组`arr1`和`arr2`, 里面的数字都是正整数. 可以用`arr2`中的数字替换掉`arr1`中的任意数字(不限次数), 问最少多少次替换能够将`arr1`改变成严格递增数组.


## 思路
### 最长递增子串, bottom-up

找LIS的长度很简单... 如果问**最少修改多少个数组元素, 使得原数组严格递增**呢?
思路是开一个数组`LIS`, `LIS[i]`表示以下标`i`结尾的**有效的**最长递增子序列长度. 有效指的是我们可以修改`i`之前的一些值, 来让数组递增到`arr[i]`.

但是有一个限制条件, `j`和`i`不但要满足`arr[j] < arr[i]`, 这两个值的`gap`还要允许放入足够的整数在中间, 也就是`arr[i] - arr[j] >= i - j`, 
否则就算`arr[i] > arr[j]`, 例如`[1,2,2,2,2,3]`中`3 > 1`但是无法通过修改中间的值(`2`)来让整个数组严格递增.

(最少的修改次数来让数组严格递增)代码
```python
def minChange(arr, n): 
    LIS = [1] * n
    for i in range(1, n): 
        for j in range(i): 
            if arr[i] > arr[j] and arr[i] - arr[j] >= i - j: 
                LIS[i] = max(LIS[i], LIS[j] + 1) 
    return n - max(LIS)
```
利用上面的思路, 对于本题, 我们可以判断`arr1`中每两个点, 是否可以利用`arr2`中的点(把`arr2`排序二分搜索, 两个坐标一左一右比一下)来补充他们之间的`gap`. 如果可以, 那么这两个点是可以**保留(不修改)**的.

还需要注意的是, 本题还要判断是否可以修改, 因为存在一些无论如何也不能变为严格递增的情况. 这样我们需要用一个`valid`数组, `valid[i]`表示是否可以处理到`i`这里.

对于例如`arr1 = [0,0,0,0,1], arr2 = [1,2,3,4,5]`, 显然全换掉就行. 但是如果单纯跑一次LIS的思路, `valid[-1]`会是`False`, 因为在原始数组缺少一个足够大的数(和足够小的数), 来作为搜索`arr2`数组的依据. 所以需要在`arr1`前后各加一个padding.

### dfs+memo, top-down
比赛的时候本来要写出来了, 然后头脑不清醒中途放弃, 想了半天别的更难想的思路...真実的菜鸡:(

对于每个点, 记录之前的数字`pre`:
 - 如果当前数字小于等于`pre`, 那么必然需要改变当前数字, 那么二分搜索`arr2`, 找比`pre`大的最小的数(Python的`bisect_right`).
如果存在, 那么这是最优解. 否则直接返回无穷大.
 - 如果当前数字大于`pre`, 那么可以修改, 也可以不改(两个分支). 修改的话和上面逻辑一样. 不修改的话, 本轮的数字作为新的`pre`继续搜. 然后返回最优的分支.
 contest的时候默认为当前大于`pre`就一定不用改了, 但是那样处理不了`arr1=[5,6,7,1], arr2=[2,3,4,5]`的情况.
 
 
 ## 代码
 
 ### 最长递增子串
 ```python
class Solution(object):
    def makeArrayIncreasing(self, arr1, arr2):
        """
        :type arr1: List[int]
        :type arr2: List[int]
        :rtype: int
        """
        arr1 = [-1] + arr1 + [float('inf')]
        arr2 = sorted(list(set(arr2)))
        left = []
        right = []
        n = len(arr1)
        for i in xrange(n):
            left.append(bisect.bisect_left(arr2, arr1[i]))
            right.append(bisect.bisect_right(arr2, arr1[i]))
        dp = [1] * n
        valid = [False] *n 
        valid[0] = True 
        for i in xrange(1, n):
            for j in xrange(i):
                if valid[j] and arr1[i] > arr1[j] and left[i] - right[j] >= i - j - 1:
                    valid[i] = True
                    dp[i] = max(dp[i], dp[j] + 1)
        return n - dp[-1] if valid[-1] else -1
```
 
### dfs+memo
```python
class Solution(object):
    def makeArrayIncreasing(self, arr1, arr2):
        """
        :type arr1: List[int]
        :type arr2: List[int]
        :rtype: int
        """
        arr2 = sorted(list(set(arr2)))
        
        memo = {}
        def dp(i, pre):
            if i == len(arr1):
                return 0
            if (i, pre) not in memo:
                if arr1[i] <= pre:
                    index = bisect.bisect_right(arr2, pre)
                    if index < len(arr2):
                        res = 1 + dp(i + 1, arr2[index])
                    else:
                        res = float('inf')
                else:
                    index = bisect.bisect_right(arr2, pre)
                    if index < len(arr2):
                        res = 1 + dp(i + 1, arr2[index])
                    else:
                        res = float('inf')
                    res = min(res, dp(i + 1, arr1[i]))
                memo[(i, pre)] = res
            return memo[(i, pre)]
        
        ret = dp(0, -1)
        return ret if ret != float('inf') else -1
```

 

