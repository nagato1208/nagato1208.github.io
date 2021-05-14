---
title: leetcode-369-Plus-One-Linked-List
date: 2019-11-26 21:39:06
categories: Leetcode_premium
tags: ['Linked list', 'math']
---
## 描述
给定一个非负整数，这个整数表示为一个非空的单链表，每个节点表示这个整数的一位。返回这个整数加一。

除了0本身，所有数字在最高位前都没有0。

列表的头节点存的是这个整数的最高位。

样例1

输入: 1 -> 2 -> 3 -> null
输出: 1 -> 2 -> 4 -> null
解释: 123 + 1 = 124


样例2

输入: 9 -> 9 -> null
输出: 1 -> 0 -> 0 -> null
解释: 99 + 1 = 100


## 思路
记录两个指针，其中r指的是当前位置，l的含义是当前最后一个不为9的数字
当r遍历到null时，我们只需要让l的next节点到null的所有节点的值全都归零，然后将l对应的值+1即可
时间复杂度O(n),n为链表长度

## 代码
```python
"""
Definition of ListNode
class ListNode(object):
    def __init__(self, val, next=None):
        self.val = val
        self.next = next
"""

class Solution:
    """
    @param head: the first Node
    @return: the answer after plus one
    """
    def plusOne(self, head):
        # Write your code here
        dummy = ListNode(0)
        dummy.next = head
        l = dummy
        r = dummy
        while r.next != None:
            r = r.next
            if r.val != 9:
                l = r
        if r.val != 9:
            r.val += 1;
        else:
            l.val += 1
            l = l.next
            while l != None:
                l.val = 0
                l = l.next
        
        if  dummy.val == 0:
            return dummy.next
        return dummy
```


