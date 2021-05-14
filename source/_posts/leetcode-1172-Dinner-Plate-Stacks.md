---
title: leetcode-1172 Dinner Plate Stacks
date: 2019-08-25 01:15:32
categories: Leetcode
tags: ['design', 'stack']
keywords: ['stack']
---
## 描述

原题在这: [https://leetcode.com/problems/dinner-plate-stacks/](https://leetcode.com/problems/dinner-plate-stacks/)

You have an infinite number of stacks arranged in a row and numbered (left to right) from 0, each of the stacks has the same maximum capacity.

Implement the DinnerPlates class:

- `DinnerPlates(int capacity)` Initializes the object with the maximum capacity of the stacks.
- `void push(int val)` pushes the given positive integer val into the leftmost stack with size less than capacity.
- `int pop()` returns the value at the top of the rightmost non-empty stack and removes it from that stack, and returns -1 if all stacks are empty.
- `int popAtStack(int index)` returns the value at the top of the stack with the given index and removes it from that stack, and returns -1 if the stack with that given index is empty.

## 思路
大概是一个双层的复合stack, 因为`popAtStack()`可能会带来一些available空间, 以后的push要优先push进leftmost的inner stack,
所以用一个最小堆来维护. 每次push之前check一下. \
时间复杂度: `O(nlogn)`

## 代码
```python
class DinnerPlates(object):

    def __init__(self, capacity):
        """
        :type capacity: int
        """
        self.a = []
        self.limit = capacity
        self.q = []
        
    def push(self, val):
        """
        :type val: int
        :rtype: None
        """
        i = self._get()
        if i != -1:
            self.a[i].append(val)
        else:
            if not self.a or len(self.a[-1]) == self.limit:
                self.a.append([])
            self.a[-1].append(val)   

    def pop(self):
        """
        :rtype: int
        """
        while self.a:
            if self.a[-1]:
                v = self.a[-1].pop()
                while self.a and not self.a[-1]:
                    self.a.pop()
                return v
            self.a.pop()
        return -1
   
    def popAtStack(self, index):
        """
        :type index: int
        :rtype: int
        """
        if index >= len(self.a) or not self.a[index]:
            return -1
        v = self.a[index].pop()
        heapq.heappush(self.q, index)
        return v
     
    def _get(self):
        while self.q:
            i = heapq.heappop(self.q)
            if i < len(self.a) and len(self.a[i]) < self.limit:
                return i
        return -1
            


# Your DinnerPlates object will be instantiated and called as such:
# obj = DinnerPlates(capacity)
# obj.push(val)
# param_2 = obj.pop()
# param_3 = obj.popAtStack(index)
```

