---
title: leetcode-1163 Last Substring in Lexicographical Order
date: 2019-08-24 00:12:32
categories: Leetcode
tags: ['string', '最小表示法']
---
## 描述
给一个字符串,返回字典序最大的substring.

## 思路
常规解法是后缀数组(倍增法), 然后拿到`sa`数组最后一个元素(最大substring起始点). 

最优解是用类似字符串最小表示法的`O(n)`解, 维护两个指针`i`,`j`表示当前正在比较的两个substring的起始点, 另一个`k`表示substring长度. 每次比较发生在`s[i+k]`和`s[j+k]`之间. match就k+1否则更新i,j.

当`k`前进一段距离, `s[i+k]`和`s[j+k]`失配时有一个特征:
 - 如果当前`s[i+k]`更大, 需要更新`j`. (我们试图用`j`找一个更大的substring起始点, 然后替换`i`). 这个时候在`[j:j+k]`闭区间的所有字符都是不大于`s[i]`的.否则我们不会等到现在才更新j,之前只要遇到比`s[i]`大的就已经更新了.
这个时候可以直接`j = j + k + 1`
 -  如果当前`s[j+k]`更大, 那么找到了一个更大的substring, 需要用`j`的值去更新`i`, 然后`j`移动到`i`的下一个位置. 
## 代码
```python
class Solution(object):
    def lastSubstring(self, s):
        """
        :type s: str
        :rtype: str
        """
        i, j, k = 0, 1, 0
        n = len(s)
        while j + k < n:
            if s[i+k] == s[j+k]:
                k += 1
                continue
            elif s[i+k] > s[j+k]:
                j = j + k + 1 
            else:
                i = j
                j = i + 1
            k = 0
        return s[i:]
```

## 倍增法(模板)
这个题用python写倍增仍然会TLE, 可能是常数项比较大, 不是单纯的`O(nlogn)`了...


```python
class Solution(object):
    def lastSubstring(self, s):
        """
        :type s: str
        :rtype: str
        """
        # x,y分别是第一关键字和第二关键字的rank
        # rank[i]: Suffix[i]在所有后缀中的排名
        # sa[i]:在i位置排着的是从哪里开始的suffix,sa[-1]就是字典序最大的substring起始点
        # rank和sa互逆
        def da(s):
            max_n = 400000
            buc = [0]*max_n # 桶
            x = [0]*max_n # key1
            y = [0]*max_n # key2
            sa = [0]*max_n
            s = [ord(c) for c in s]
            n = len(s)
            max_v = max(s) + 1 # 最大的char + 1
            # 第一轮基数排序:一位
            for i in xrange(n):
                x[i] = s[i] # key1就是s里面的字符值本身
                buc[s[i]] += 1
            for i in xrange(1, max_v):
                buc[i] += buc[i-1]
            for i in xrange(n-1, -1, -1):
                buc[s[i]] -= 1
                sa[buc[s[i]]] = i

            k = 1
            while k <= n:
                p = 0
                for i in xrange(n-k, n):
                    y[p] = i
                    p += 1
                for i in xrange(n):
                    # sa其实是从小到大排列的后缀们,如果sa[i]>=k说明可以作为拼接的后半段
                    if sa[i] >= k:
                        y[p] = sa[i] - k # p作为指针引导y数组的填充
                        p += 1
                for i in xrange(max_v):
                    buc[i] = 0 # clear
                for i in xrange(n):
                    # 有一种.sort(lambda suffix: (suffix.x, suffix.y))的感觉
                    buc[x[y[i]]] += 1
                for i in xrange(1, max_v):
                    buc[i] += buc[i-1]
                for i in xrange(n-1, -1, -1):
                    buc[x[y[i]]] -= 1
                    sa[buc[x[y[i]]]] = y[i]
                x, y = y[:], x[:]
                p = 1
                x[sa[0]] = 0
                for i in xrange(1, n):
                    if y[sa[i-1]] == y[sa[i]] and y[sa[i-1] + k] == y[sa[i] + k]:
                        x[sa[i]] = p - 1
                    else:
                        x[sa[i]] = p
                        p += 1
                if p >= n: # 去重的时候没有发现重复元素,说明排序彻底完成了
                    break
                max_v = p # 下次基数排序的最大值
                k <<= 1
            return sa[:n]
        
        return s[da(s)[-1]:]
```
