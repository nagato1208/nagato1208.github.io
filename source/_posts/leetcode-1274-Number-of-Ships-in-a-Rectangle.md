---
title: leetcode-1274-Number of Ships in a Rectangle
date: 2019-12-01 17:56:57
categories: Leetcode_premium
tags: ['interact', 'divide and conquer']
---

## 描述
On the sea represented by a cartesian plane, each ship is located at an integer point, and each integer point may contain at most 1 ship.

You have a function `Sea.hasShips(topRight, bottomLeft)` which takes two points as arguments and returns true if and only if there is at least one ship in the rectangle represented by the two points, including on the boundary.

Given two points, which are the top right and bottom left corners of a rectangle, return the number of ships present in that rectangle.  It is guaranteed that there are **at most 10 ships in that rectangle**.

Submissions making more than 400 calls to `hasShips` will be judged Wrong Answer.  Also, any solutions that attempt to circumvent the judge will result in disqualification.

Example:
```ignorelang
Input: 
ships = [[1,1],[2,2],[3,3],[5,5]], topRight = [4,4], bottomLeft = [0,0]
Output: 3
Explanation: From [0,0] to [4,4] we can count 3 ships within the range.
```

Constraints:
```ignorelang
On the input ships is only given to initialize the map internally. You must solve this problem "blindfolded". In other words, you must find the answer using the given hasShips API, without knowing the ships position.
0 <= bottomLeft[0] <= topRight[0] <= 1000
0 <= bottomLeft[1] <= topRight[1] <= 1000
```

## 思路
1. 每次按照中心点分成4块(左上, 左下, 右上, 右下), 然后递归检查这四块, 如果`hasShips`返回`false`就直接跳过.
2. hack: Python的话可以用`print sea.__dict__`直接打印出`sea`这个class的所有属性, 对于样例来说, 结果是`{'_Sea__guesses': 400, '_Sea__ans': [[1, 1], [2, 2], [3, 3], [5, 5]]}`. 相当于保存答案的字典已经拿到了. 直接用`sea._Sea__ans`可以access到.

## 代码
```python
# """
# This is Sea's API interface.
# You should not implement it, or speculate about its implementation
# """
#class Sea(object):
#    def hasShips(self, topRight, bottomLeft):
#        """
#        :type topRight: Point
#		 :type bottomLeft: Point
#        :rtype bool
#        """
#
#class Point(object):
#	def __init__(self, x, y):
#		self.x = x
#		self.y = y

class Solution(object):
    def countShips(self, sea, tr, bl):
        """
        :type sea: Sea
        :type topRight: Point
        :type bottomLeft: Point
        :rtype: integer
        """
        if tr.x==bl.x and tr.y==bl.y:
            return 1 if sea.hasShips(tr, bl) else 0
        mx = (tr.x + bl.x) / 2
        my = (tr.y + bl.y) / 2
        res = 0
        if mx>=bl.x and my>=bl.y and sea.hasShips(Point(mx, my), bl):
            res += self.countShips(sea, Point(mx, my), bl)
        if mx>=bl.x and tr.y>=my+1 and my+1<=1000 and sea.hasShips(Point(mx, tr.y), Point(bl.x, my+1)):
            res += self.countShips(sea, Point(mx, tr.y), Point(bl.x, my+1))
        if tr.x>=mx+1 and my>=bl.y and my+1<=1000 and sea.hasShips(Point(tr.x, my), Point(mx+1, bl.y)):
            res += self.countShips(sea, Point(tr.x, my), Point(mx+1, bl.y))
        if tr.x>=mx+1 and tr.y>=my+1 and mx+1<=1000 and my+1<=1000 and sea.hasShips(tr, Point(mx+1, my+1)):
            res += self.countShips(sea, tr, Point(mx+1, my+1))
        return res
```


```python
class Solution(object):
    def countShips(self, sea, tr, bl):
        """
        :type sea: Sea
        :type topRight: Point
        :type bottomLeft: Point
        :rtype: integer
        """
        return sum(bl.x <= x <= tr.x and bl.y <= y <= tr.y for x, y in sea._Sea__ans)
```


