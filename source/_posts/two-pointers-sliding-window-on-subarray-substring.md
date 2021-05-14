---
title: Leetcode-subarray和substring上面的双指针/滑动窗口
date: 2019-11-26 15:10:28
categories: Leetcode
tags: ['two pointers']
---

## 3. Longest Substring Without Repeating Characters

给定一个字符串，返回其中不含有重复字符的**最长子串**的长度。
```python
class Solution(object):
    def lengthOfLongestSubstring(self, s):
        """
        :type s: str
        :rtype: int
        """
        res = 0
        left = 0
        count = collections.defaultdict(int)
        for right, char in enumerate(s):
            count[char] += 1
            while count[char] > 1:
                count[s[left]] -= 1
                left += 1
            # right - left +_1 表示以当前的right下标结尾的substring中,满足条件的最长长度
            res = max(res, right - left + 1)
        return res
```

## 159. Longest Substring with At Most Two Distinct Characters

给定一个字符串，找出最长子串`T`的长度，`T`最多包含2个不同的字符
```python
class Solution:
    """
    @param s: a string
    @return: the length of the longest substring T that contains at most 2 distinct characters
    """
    def lengthOfLongestSubstringTwoDistinct(self, s):
        # Write your code here
        count = collections.defaultdict(int)
        left = 0
        res = 0
        for right,char in enumerate(s):
            count[char] += 1   
            while len(count) > 2:
                count[s[left]] -= 1 
                if count[s[left]] == 0:
                    count.pop(s[left])
                left += 1   
            # right - left + 1 表示以当前的right下标结尾的substring中,满足条件的最长长度
            res = max(res, right - left + 1)     
        return res 
```

## 340. Longest Substring with At Most K Distinct Characters
把上一题的`2`改成`k`就是本题. 同样,代码也只需把`2`改成`k`.
```python
class Solution:
    """
    @param s: A string
    @param k: An integer
    @return: An integer
    """
    def lengthOfLongestSubstringKDistinct(self, s, k):
        # write your code here
        count = collections.defaultdict(int)
        left = 0
        res = 0
        for right,char in enumerate(s):
            count[char] += 1   
            while len(count) > k:
                count[s[left]] -= 1 
                if count[s[left]] == 0:
                    count.pop(s[left])
                left += 1   
            # right - left + 1 表示以当前的right下标结尾的substring中,满足条件的最长长度
            res = max(res, right - left + 1)     
        return res 
```

## 713. Subarray Product Less Than K
统计一个array里面有多少个subarray的内部元素(都是正整数)之积小于K. 基本和上面的`AtMostK()`一模一样, 加上等号就行.
```python
class Solution(object):
    def numSubarrayProductLessThanK(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        if k<=1: return 0
        cur = 1
        res = 0
        left = 0
        for right, val in enumerate(nums):
            cur *= val
            while cur >= k:
                cur /= nums[left]
                left += 1
            res += right - left +1
        return res
```

## 992. Subarrays with K Different Integers
返回**具有`k`个不同字符的subarray**的数量. 可以用上一题`AtMostK()`的模板.
类似`AtMostK()`的题目大都可以用双指针, 然后要求`exactly K`的话, 就求一个prefix difference.
```python
class Solution(object):
    def subarraysWithKDistinct(self, A, K):
        """
        :type A: List[int]
        :type K: int
        :rtype: int
        """
        return self.atMostK(A, K) - self.atMostK(A, K-1)
        
    def atMostK(self, A, K):
        res = 0
        left = 0
        count = collections.Counter()
        for right, char in enumerate(A):
            count[char] += 1
            while len(count) > K:
                    count[A[left]] -= 1
                    if count[A[left]] == 0:
                        count.pop(A[left])
                    left += 1
            # 以下标right结尾的满足条件的subarray的数量 right - left
            res += right - left
        return res
```

## 395. Longest Substring with At Least K Repeating Characters
Find the length of the longest substring T of a given string (consists of lowercase letters only) such that every character in `T` appears no less than `k` times.
Example:
```ignorelang
Input:
s = "ababbc", k = 2

Output:
5

The longest substring is "ababb", as 'a' is repeated 2 times and 'b' is repeated 3 times.
```

对于`AtLeastK`的题其实不能直接双指针,因为情况太多了,一般是某种metric超过限制, 才能收缩left指针, 但是这个题, 每种char出现的次数似乎多多益善.

为了构造双指针的条件, 可以枚举所有可能的candidate substring中的unique的char个数(题目限制全是lower case English letters所以最大是26). 然后题目变成了, 对于含有`AtMostK`(这里的大写`K`指的是`max_unique`)个字符的substring,满足最小频次>=`k`的substring的最大长度. 相当于求了26次最大长度然后取最大.

```python
class Solution(object):
    def longestSubstring(self, s, k):
        """
        :type s: str
        :type k: int
        :rtype: int
        """
        res = 0
        # 枚举AtMostK里面的K也就是max_unique, 用来make two pointers work.
        for max_unique in range(1, 27):
            count = collections.Counter()   # largest size is 26
            left = 0
            for right, char in enumerate(s):
                count[char] += 1
                while len(count) > max_unique:
                    count[s[left]] -= 1
                    if count[s[left]] == 0:
                        count.pop(s[left])
                    left += 1  
                if min(count.values()) >= k:
                    res = max(res, right - left + 1)
        return res
```







