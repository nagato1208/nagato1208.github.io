---
title: leetcode-218 The Skyline Problem
date: 2019-08-25 03:03:16
categories: Leetcode
tags: ['divide and conquer', 'heap', 'BIT', 'segment tree']
---

## 描述
原题在这: [https://leetcode.com/problems/the-skyline-problem/](https://leetcode.com/problems/the-skyline-problem/)

## 思路

### 分治

分为divide和merge两部分, divide比较简单, 一直对半分, 直到`len(buildings) <= 1`. 

对于merge阶段, 首先这个时候左侧和右侧的list都是要返回的结果的格式即`[index, height]`. 现在要做的是merge为一组:
- 用两个变量`l`, `r`分别表示正在处理的左侧和右侧list的下标.
- 用两个变量`h1`, `h2`分别表示左侧和右侧的当前高度.
- 对于左侧和右侧各自的`[index, height]`, 谁的`index`更小, 就在结果中放入它, 同时更新对应的下标, `l++`或者`r++`.(类比`merge two sorted lists`)
- 对于左侧和右侧各自的`[index, height]`, 如果`index`相等, 说明有两个(考虑到后面尚未比较的, 可能有更多)`building`都从这里起始, 那么在结果放入`height`的时候,选择更大的一个`height`:`max(h1, h2)`, 然后同时`l++`和`r++`.
- (接上条) `index`不同时同理, 因为`l`和`r`都是当前有效的高度, 那么每次往结果里面放`height`, 都要放`max(h1, h2)`.
- 及时更新`h1`和`h2`.
- 在结果中加入新的值时, check一下要放入的`height`是不是和上一个`height`相等, 相等的话直接跳过. 


### 堆
首先把所有`buildings`分割成两组`events`并合并, 分别是`[left, height, right]`表示当前`building`起始于`left`终止于`right`, 高度是`height`, 以及`[right, 0, -1]`表示从`right`开始高度是`0`, 结束点不重要.

- `height`取负值, 因为排序时对于同样起始点的`event`, 我们希望先处理实际高度更大的.
- `events`排好序后开始处理, 如果发现当前的`height`不是0, 说明这是起始点不是终止点. 那么这个`height`在一定范围内是有效的. 把失效点`right`和`height`一起放入堆中. Python默认最小堆, 所以我们放入的实际是取反后的`height`来模拟最大堆. 
- 最大堆的作用: 提供**当前尚未失效的最大高度**.
- 每次遇到新的坐标, 先pop出所有失效的高度(失效点早于当前点).


### 线段树/BIT [待完善]
假设初始时天际线是一条高度为0的水平线, 每次遇到一个`building`, 就更新`[left,right]`区间内的最大高度. 然后从左到右, 对每个点query一下最大高度.

线段树大概长这样:(都是闭区间)
```python
                            [0,7](1)
           [0,3](2)                           [4,7](3)
   [0,1](4)         [2,3](5)          [4,5](6)         [6,7](7) 
[0](8) [1](9)   [2](10) [3](11)   [4](12) [5](13)   [6](14) [7](15)    
```
- 用一个数组来存线段树的所有节点, 对于每个节点的下标`node`, `node<<1`就是左侧子节点(表示当前范围的左侧一半), `(node<<1)+1`是右侧子节点.
- `range update(new value)` 给`[l, r]`代表的范围内整体赋值, 需要懒标记. 每次从外界call这个函数,入口节点下标是`1`, 这个时候有效范围(`1`号节点代表的)是`[0,n]`, 然后在update函数中,不断二分缩小范围直到完全匹配`[l,r]`.然后赋值(本题求`max`), 在完全匹配后, 匹配的这个节点完成更新, 可以立即停止, 不需要继续更新下面的(所有子节点都是满足`[l,r]`范围条件的, 不需要全部都更新一遍了).
- `single point query`单点查询. 不断二分缩小范围直到当前范围长度为1,exactly match我们要找的点坐标.
- 因为用了懒标记, 上层永远比下层更新更及时. 直接query到的可能没被更新过, 所以每次match到下层, 还要和上层的取`max`.
- 生成结果时, query这个区间内的最大height. 因为坐标绝对值可能很大, 需要离散化.



## 代码

### 分治
```python
class Solution(object):
    def getSkyline(self, buildings):
        """
        :type buildings: List[List[int]]
        :rtype: List[List[int]]
        """
        
        def do(buildings):
            n = len(buildings)
            if n == 0:
                return []
            if n == 1:
                return [[buildings[0][0], buildings[0][2]], [buildings[0][1], 0]]
            return merge(do(buildings[:n/2]), do(buildings[n/2:]))
        
        def merge(res1, res2):
            l = r = 0
            h1 = h2 = 0
            res = []
            while l < len(res1) and r < len(res2):
                if res1[l][0] < res2[r][0]:
                    h1 = res1[l][1]
                    if not res or res[-1][1] != max(h1, h2):
                        res.append([res1[l][0], max(h1, h2)])
                    l += 1
                elif res1[l][0] > res2[r][0]:
                    h2 = res2[r][1]
                    if not res or res[-1][1] != max(h1, h2):
                        res.append([res2[r][0], max(h1, h2)])
                    r += 1
                else:
                    h1, h2 = res1[l][1], res2[r][1]
                    if not res or res[-1][1] != max(h1, h2):
                        res.append([res1[l][0], max(h1, h2)])
                    l += 1
                    r += 1
            while l < len(res1):
                if not res or res[-1][1] != res1[l][1]:
                    res.append(res1[l])      
                l += 1
            while r < len(res2):
                if not res or res[-1][1] != res2[r][1]:
                    res.append(res2[r])
                r += 1
            return res
        
        return do(buildings)
```
### 堆

```python
class Solution(object):
    def getSkyline(self, buildings):
        """
        :type buildings: List[List[int]]
        :rtype: List[List[int]]
        """
        events = []
        for l, r, h in buildings:
            events.append([l, -h, r])
            events.append([r, 0, -1])
        events.sort()
        res, hp = [], [(0, float("inf"))]
        for l, h, r in events:
            while l >= hp[0][1]: 
                heapq.heappop(hp) 
            if h:
                heapq.heappush(hp, (h, r))
            if not res or res[-1][1] != -hp[0][0]: 
                res.append([l, -hp[0][0]])
        return res    
```

### 线段树/BIT
```python
class segTree():
    def __init__(self, n):
        self.tree = [0]*n
    def update(self, index, start, end, left, right, value): # update value of[left,right] inside [start,end]
        if left > right or right < start or left > end: 
            return
        if start == left and end == right:
            self.tree[index] = max(self.tree[index], value)
            return
        mid = (start + end) / 2
        self.update(index*2, start, mid, left, min(mid, right), value)
        self.update(index*2+1, mid+1, end, max(mid+1, left), right, value)
    def query(self, index, start, end, i): # 外面call这个query的时候,index永远是1,相当于入口点
        if start == end:
            return self.tree[index]
        mid = (start + end) / 2  # 我们要找第i个
        res = self.query(index*2, start, mid, i) if i <= mid else self.query(index*2+1, mid+1, end, i)
        return max(res, self.tree[index]) #lazy?
        

class Solution(object):
    def getSkyline(self, buildings):
        """
        :type buildings: List[List[int]]
        :rtype: List[List[int]]
        """   
        indices = set()
        for s,e,h in buildings:
            indices.add(s)
            indices.add(e)
        indices = sorted(list(indices))
        index2id = {n:i for i,n in enumerate(indices)}
        END = len(indices) - 1
        st = segTree(len(indices)*4) # maxn是题目给的最大区间,而节点数要开4倍,确切的来说节点数要开大于maxn的最小2x的两倍
        for s,e,h in buildings:
            st.update(1, 0, END, index2id[s], index2id[e]-1, h)
            # 有的房子右边的点可能和其他更低的房子相交，所以右边坐标下标 -1−1 再插入（更新）,(range update并不考虑是起始还是结束)
        pre = 0
        res = []
        for i in xrange(END + 1):
            h = st.query(1, 0, END, i)   # 在0到END(包含)内,找ith 区间max height
            if h != pre:
                res.append([indices[i], h])
            pre = h
        return res
```
