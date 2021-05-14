---
title: codeforces-221D Little Elephant and Array题解
date: 2019-09-04 11:51:15
categories: Codeforces
tags: ['离线算法', 'math', 'brute force']
---

## 描述
给一个数组`nums`, 和一些query(连续到来). 每个query都是`[l, r]`的格式(闭区间左右端点). 对于每个query, 求出在这个区间内的数中, 出现次数等于本身值的数的个数. 比如`[2,2,3,3,3]`, `2`出现了`2`次, `3`出现了`3`次, 那么结果是`1+1=2`.
数组长度和query个数各自都是最大`100000`. 


## 思路
并没有什么思路, 用线段树可解但是判断条件太复杂了...
 
不妨思考一下暴力解, 普通的暴力解就是每个query都统计一下那个区间内的数字和对应的频数. 这样复杂度是`O(n**2)`, 肯定过不了OJ. 然后思考query规则, 对于数字`x`, 她需要出现`x`次, 那么对于长度最大`100000`的数组, 如果整体query(`[0, length]`)的话, 最大可能的返回值是多少呢? 
`1(1个1, 不能更多了, 否则会浪费名额, 相当于降低100000这个最大值) + 2(2个2) + 3 + ... + n <= 100000`, `n`最大`446`左右. 对于`446*100000`的复杂度, 勉强可以接受. 

所以我们可以枚举所有在`nums`中的数字, 只有当前数字自身比它出现次数小时(反之, 在任何一个小区间也不可能`x == count[x]`), 计算一下它的次数前缀和, 然后对于所有query, 计算这个数字对每个query的贡献.
 
 因为不是实时处理query而是集中处理, 集中返回答案, 属于离线算法. 
 
 
## 代码
```python
import collections
def do():
    n, n_query = map(int, input().split(" "))
    N = max(n, n_query) + 1
    nums = [0] * N
    count = collections.defaultdict(int)
    tmp = [int(c) for c in input().split(" ")]
    for i, num in enumerate(tmp):
        nums[i+1] = num
        if num <= n:   # 如果数字自身比数组长度大, 那么它永远不可能出现在query结果中, 忽略掉
            count[num] += 1
    l = [0] * N
    r = [0] * N
    s = [0] * N
    res = [0] * N
    for i in range(1, n_query + 1):
        l[i], r[i] = map(int, input().split(" "))
    for num in set(nums):
        if num <= count[num]: # 只有数字自身比出现次数小时, 才会进入下面的循环,最多446个数进来
            for i in range(1, n + 1):
                s[i] = s[i - 1] + (nums[i] == num)  # 对于num这个数字统计出现次数的前缀和
            for j in range(1, n_query + 1):
                if s[r[j]] - s[l[j] - 1] == num:  # 如果第j个query满足了条件, 那么num给res[j]贡献了一个点
                    res[j] += 1
    for i in range(1, n_query + 1):
        print(res[i] - 1)
 
do()
```