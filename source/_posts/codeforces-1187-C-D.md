---
title: codeforces-1187-C-D总结
date: 2019-09-01 21:29:47
categories: Codeforces
tags: ['segment tree', 'math', 'construct', 'greedy']
---
2019年9月1日CF群打卡题: 1187C, 1187D.

## 1187C Vasya And Array
### 描述
有一个未知数组`nums`, 现在已知的信息是一些三元组`a`.
- `a[0] == 1`表示`nums`数组的`[a[1], a[2]]`闭区间是非递减的.
- `a[0] == 0`表示`nums`数组的`[a[1], a[2]]`闭区间不是非递减的(有点绕, 其实就是这段区间内至少存在一个递减的地方`nums[i-1]>nums[i]`).

利用已知信息, 建造一个符合条件的`nums`. 如果这样的数组不存在, 打印`NO`.

### 思路
对于`a[0] == 1`的情况, `nums`数组的`(a[1], a[2]]`左开右闭区间内所有下标`i`, 必须都满足`nums[i-1] <= nums[i]`(可以看做一种限制或者标记). 

先用一个数组存这些标记. 然后对于每一个`a[0] == 0`的情况, 搜索`[a[1], a[2]]`, 如果找不到任何一个未被标记的, 说明此区间内必须非递减, 不能满足要求, 可以直接返回`NO`.

为了表示简单, 可以把非递减当做不变, 在每一个必须递减的地方, 我们给上一个值减掉1来填充当前值.

### 代码
```python
def do():
    N, n = map(int, input().split(" "))
    d = []
    flag = [1] * N   #默认允许递减
    for _ in range(n):
        f, s, e = map(int, input().split(" "))
        d.append([f, s, e])
        if f:
            for i in range(s, e):
                flag[i] = 0 # 这个i处不允许递减
    for f, s, e in d:
        if not f:
            valid = any(flag[i] for i in range(s, e)) # 检查这个区间内允不允许递减(至少一个地方)
            if not valid:
                print("NO")
                return 0
    cur = 1001
    res = []
    for i in range(1, N+1):
        cur -= flag[i-1]   # 允许递减就减, 否则不变
        res.append(cur)
    print("YES")
    print(" ".join(str(c) for c in res))
    return 0
do()
```

## 1187D Subarray Sorting

### 描述
给一个数组`a`和目标数组`b`, 对于`a`来说, 允许任意次, 任意subarray的从小到大排序. (比如对`a`的subarray `a[1:4]`排序, 其他位置不变).
问是否可以从`a`转换成`b`(`YES` or `NO`).

### 思路
类比冒泡排序, 每次排序完成后, (这个区间内的)最小值都会跑到区间最前边. 不是最小的值会被比它小的挡住, 不会跑到最前边. ~~废话~~

比如, 对于`b[0]`来说, 需要找到`b[0]`在`a`中出现的第一个位置`i`, 然后排序`a[0:i+1]`. 
- 如果`b[0]`是`a[0:i+1]`的最小值, 那么`b[0]`就由`a`产生了. 
- 否则, 有更小的值存在于`[0,i-1]`区间内, 阻止了`a[i]`在排序过程中的前进. (想一想, 最早出现的`a[i]`都不行, 后面的更不行了, 同样会被`[0,i-1]`内的更小值挡住, 甚至`i`后面还有更多的更小值). 这样,`b[0]`永远也不能由排序得到. 结果一定是`NO`.
- `b[0]`由`a`成功产生的话, 对于`b[1]`做同样的操作. 需要注意的是, 每次用完`a`中的一个数, 都要把它置为无穷大, 不会影响以后的区间最小值查询.
- 所以本题化简为了一个单点更新和区间查询的问题. (线段树RMQ). (类比skyline problem, 那个是区间更新, 单点查询)

### 代码
会TLE, 但是所有长度短于`300000`的case都能过...正确性没问题, CF的OJ对Python不友好.
```python
import collections
class segTree():
    def __init__(self, nums, n):
        self.nums = nums
        self.t = [0] * (n << 2)
 
    def build(self, node, l, r):
        if l == r:
            self.t[node] = self.nums[l]
            return self.t[node]
        mid = (l + r) >> 1
        left = self.build(node*2, l, mid)
        right = self.build(node*2 + 1, mid + 1, r)
        self.t[node] = min(self.t[node*2], self.t[node*2 + 1])
        return self.t[node]
 
    def update(self, node, l, r, index, value):
        if l == r:
            self.t[node] = value
            return
        mid = (l + r) >> 1
        if index <= mid:
            self.update(node*2, l, mid, index, value)
        else:
            self.update(node*2 + 1, mid + 1, r, index, value)
        self.t[node] = min(self.t[node*2], self.t[node*2 + 1])
 
    def query(self, node, start, end, l, r):
        if l > r or r < start or l > end:
            return float('inf')
        if start == l and end == r:
            return self.t[node]
        mid = (start + end) >> 1
        res_l = self.query(node*2, start, mid, l, min(mid, r))
        res_r = self.query(node*2+1, mid+1, end, max(mid+1, l), r)
        return min(res_l, res_r)
 
def do():
    N = int(input())
    res = []
    for _ in range(N):
        n = int(input())
        nums1 = [int(c) for c in input().split(" ")]
        nums2 = [int(c) for c in input().split(" ")]
        st = segTree(nums1, n)
        st.build(1, 0, n - 1)
        d = collections.defaultdict(list)
        for i, num in enumerate(nums1[::-1]):
            d[num].append(n - i - 1)
        f = True
        for num in nums2:
            if not d[num]:
                f = False
                break
            cur = d[num].pop()
            qmin = st.query(1, 0, n-1, 0, cur)
            if qmin != num:
                f = False
                break
            st.update(1, 0, n-1, cur, float('inf'))
        res.append(f)
 
    for f in res:
        print("YES" if f else "NO")
 
do()
```


