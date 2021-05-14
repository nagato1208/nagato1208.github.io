---
title: codeforces-1208D Restore Permutation题解
date: 2019-08-30 02:28:41
categories: Codeforces
tags: ['greedy', 'segment tree']
---

## 描述
对于`[1, n]`闭区间的所有整数的某个permutation `permutation`, 我们有数组`nums`. 定义`nums`的每个元素`nums[i]`表示在permutation的下标`i`之前的比`permutation[i]`小的所有数的和.
例如如果`nums`是`0 0 0`, 那么`permutation`是`[3,2,1]`. 因为对于每个数, 没有在它之前的比它小的数. 如果`nums`是`0 1 1 1 10`, `permutation`是`[1,4,3,2,5]`.

## 思路

考虑`nums`数组的最后一个元素. 因为这是数组的最后, 所有比它(放在这个位置的`[1,n]`内的某个数字)小的数字都会出现在它前面. (不可能出现在后面因为这已经是最后了). 假设这个最后应该放置的数字是`k`, 那么这些比它的小的数字之和必然是`sum([1...k-1])
`.
对于上面的式子可能看做是一种前缀和. 所以通过搜索(这个前缀和在所有前缀和中的位置)可以找到到`k`的值. 

比如, 对于`[1,4,2,3]`这个`permutation`来说, `nums`是`[0,1,1,3]`. 最后一个值是`3`,由`1+2`得到, 那么放在这个位置的数一定是`1,2`后面的第一个数字`3`. 拿到`3`之后, 剩余的数字`1,2,4`起始也可以看做一个permutation, 只要有序无重复就行.(它仍然拥有一个对应的前缀和数组). 但是上一个前缀和数组含有`3`的影响, 现在已经找到了`3`,可以把它删掉(置为`0`).

需要动态维护这样一个前缀和数组, 使用了线段树(权值线段树). 每个节点存的是`[l, r]`范围内的和. 首先把`[1,n]`所有数插入进去, 然后query当前`nums`数组的最后一个值. 返回的值就是应该放在这个位置的数字, 然后把这个数字置为`0`. 需要注意的是, 这样我们的前缀和数组的生成就会有很多`0`的影响, eg. `[1,3,3,3,3,3,6]`, 因为原始数组出现了多个0, 前缀和数组会有重复数字. 这个时候再查找目标前缀和, 我们需要找到尽量右侧的点(坐标).类似`bisect.bisect_right()`. 

所以实际query时, 参数是`nums[i]+1`, 保证不受`0`的干扰. (其实还有一种情况, `[1,2,3]`的前缀和是`[1,3,6]`) 搜`6`时会落到`3`上, 但和`6`对应的是`4`.

## 代码
```python
class segTree():
    def __init__(self, n):
        self.t = [0] * (n << 2)
 
    def update(self, node, l, r, index, value):
        if l == r:
            self.t[node] = value
            return
        mid = (l + r) >> 1
        if index <= mid:
            self.update(node*2, l, mid, index, value)
        else:
            self.update(node*2 + 1, mid + 1, r, index, value)
        self.t[node] = self.t[node*2] + self.t[node*2 + 1]    # 更新完两个子节点还要计算一下当前节点
 
    def query(self, node, l, r, value):
        if l == r:
            return self.t[node]
        mid = (l + r) >> 1
        if self.t[node*2] >= value:
            return self.query(node*2, l, mid, value)
        return self.query(node*2 + 1, mid + 1, r, value - self.t[node*2])   # 向右搜索时, 减掉左侧节点的值, 这样最终会落到目标点上
 
def do():
    n = int(input())
    nums = [int(i) for i in input().split(" ")]
    res = [0]*n
    weightTree = segTree(n)
    for i in range(1, n+1):
        weightTree.update(1, 1, n, i, i)
    # print(weightTree.t)
    for i in range(n-1, -1, -1):
        res[i] = weightTree.query(1, 1, n, nums[i] + 1)
        weightTree.update(1, 1, n, res[i], 0)
    return " ".join([str(c) for c in res])
 
print(do())
```
