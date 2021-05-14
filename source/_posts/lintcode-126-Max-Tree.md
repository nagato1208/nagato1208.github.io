---
title: lintcode-126. Max Tree
date: 2019-09-26 19:29:15
categories: Lintcode
tags: ['stack', 'tree']
---

## 描述
Given an integer array with no duplicates. A max tree building on this array is defined as follow:

- The root is the maximum number in the array
- The left subtree and right subtree are the max trees of the subarray divided by the root number.

Construct the max tree by the given array in `O(n)` time/space complexity. 

Example 1:
```ignorelang
Input：[2, 5, 6, 0, 3, 1]
Output：{6,5,3,2,#,0,1}
Explanation：
the max tree constructed by this array is:
    6
   / \
  5   3
 /   / \
2   0   1
```
Example 2:
```ignorelang
Input：[6,4,20]
Output：{20,6,#,#,4}
Explanation： 
     20
     / 
    6
     \
      4
```

## 思路
维护一个单调递减单调栈. 如果当前值比`stack[-1]`要大, 循环弹出栈顶. 当前值是第一个比在弹出值右侧并且比它大的. 所以更新当前节点的左子节点.
弹出完毕, 如果此时栈非空, 说明栈顶是在当前值左侧第一个比它大的. 栈顶的右子节点应该是当前节点.


## 代码
```python
"""
Definition of TreeNode:
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left, self.right = None, None
"""
        

class Solution:
    """
    @param A: Given an integer array with no duplicates.
    @return: The root of max tree.
    """
    def maxTree(self, A):
        stack = []
        for a in A:
            cur = TreeNode(a)
            while stack and stack[-1].val < cur.val:
                cur.left = stack.pop()     # 比如这里我们先后pop出a,b; cur.left先被赋值a,然后赋值b(此时a在b的右子树上)
            if stack:
                stack[-1].right = cur
            stack.append(cur)
        return stack[0]
```