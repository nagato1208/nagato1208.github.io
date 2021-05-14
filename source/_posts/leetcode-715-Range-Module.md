---
title: leetcode-715 Range Module
date: 2019-08-25 03:04:23
categories: Leetcode
tags: ['binary search', 'design']
---
## 描述
实现一个数据结构, 支持对区间的动态tracking, 分别有以下三个操作:
- `addRange(int left, int right)` 新增一个左闭右开的tracking区间`[left, right)`.
- `queryRange(int left, int right)`check区间`[left, right)`是否正在被tracking, 返回布尔值.
- `removeRange(int left, int right)` 取消tracking这个区间.

## 思路
维护一个list`index`存当前有效的离散点. 这样这个list有以下特征:
- `index`必然是坐标`left`和`right`两两成组的interval list, 在加入和删除的过程中, 会自动merge.
- `addRange()`: `left`向左二分搜索插入点`l`(下标), `right`向右二分搜索插入点`r`. 如果`l`是偶数, 说明在它之前坐标们是两两成对的(可以在纸上画画), 当前点`left`没有落入任何一个已有的区间内, 对于`left`来说是一个独立点, 必然会存在与本轮更新后的`index`
中, 那么加入`new_index`. `right`同理. 最后更新`index`的`[l:r]`段为`new_index`.
- `queryRange()`和`addRange()`稍有差别. `left`向右二分搜索插入点`l`(下标), `right`向左二分搜索插入点`r`. 如果`l == r`, 说明这个被query的区间落在了`index`的离散点们标示出的**一个**(而不是多个)区间内.同时`l`和`r`需要满足是奇数, 否则query区间实际落入的是gap,而不是`index`标识的tracking区间.
- `removeRange()`基本是`addRange()`的逆操作. 当`r`是奇数,说明在它之前坐标们**没有**两两成对, 这个点落入了已有区间中, 那么它在删除操作后会是一个新的区间边界点. 需要加入新的`index`中. `right`同理.

## 代码
```python
class RangeModule(object):

    def __init__(self):
        self.index = []
        
    def addRange(self, left, right):
        """
        :type left: int
        :type right: int
        :rtype: None
        """
        l = bisect.bisect_left(self.index, left)
        r = bisect.bisect_right(self.index, right)
        new_index = []
        if l%2==0:
            new_index.append(left)
        if r%2==0:
            new_index.append(right)
        self.index[l:r] = new_index        

    def queryRange(self, left, right):
        """
        :type left: int
        :type right: int
        :rtype: bool
        """
        l = bisect.bisect_right(self.index, left)
        r = bisect.bisect_left(self.index, right)
        return l == r and l%2      

    def removeRange(self, left, right):
        """
        :type left: int
        :type right: int
        :rtype: None
        """
        l = bisect.bisect_left(self.index, left)
        r = bisect.bisect_right(self.index, right)
        new_index = []
        if l%2==1:
            new_index.append(left)
        if r%2==1:
            new_index.append(right)
        self.index[l:r] = new_index        
        
# Your RangeModule object will be instantiated and called as such:
# obj = RangeModule()
# obj.addRange(left,right)
# param_2 = obj.queryRange(left,right)
# obj.removeRange(left,right)
```