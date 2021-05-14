---
title: codeforces-1208-A-B-C总结
date: 2019-08-27 22:36:52
categories: Codeforces
tags: ['bit', 'math', 'two pointers']
---
2019年8月27日CF群打卡题: 1208A, 1208B, 1208C
- https://codeforces.com/problemset/problem/1208/A/
- https://codeforces.com/problemset/problem/1208/B/
- https://codeforces.com/problemset/problem/1208/C/

## 1208A XORinacci

### 描述
XOR版本的Fibonacci数列. 输入两个起始数`a`和`b`, 和要输出的第n个数(0-indexed). 输出该数. 

### 思路
n的范围`[0, 10**9]`, 肯定不能常规解. 观察每一位可以发现bit`0`和`1`是循环的. 循环节永远是`1-1-0`
 - 0 - 0 - 0 - 0 - 0 - 0 - 0 ... (初始`a=0, b=0`)
 - 0 - 1 - 1 - 0 - 1 - 1 - 0 ... (初始`a=0, b=1`)
 - 1 - 1 - 0 - 1 - 1 - 0 - 1 ... (初始`a=1, b=1`)
 - 1 - 0 - 1 - 1 - 0 - 1 - 1 - 0 ... (初始`a=1, b=0`)
结果是32位整数, 分别每一位处理.

### 代码
```python
def solve(a, b, n):
    if n==0: return a
    if n==1: return b
    res = 0
    for i in range(32):
        x = a & (1<<i)
        y = b & (1<<i)
        if x:
            if y:
                res |= ((n%3!=2)<<i)
            else:
                res |= ((n%3!=1)<<i)
        elif y:
                res |= ((n%3!=0)<<i)
    return res
 
def do():
    n = int(input())
    for _ in range(n):
        a, b, n = map(int, input().split(" "))
        print(solve(a, b, n))
do()
```

## 1208B Uniqueness
### 描述
给一个`nums` array,允许最多删掉一段(连续的)subarray, 删掉的这段最短多长, 使得剩下的部分没有重复数字.

### 思路
因为允许删除中间的一段, 我一开始的思路是直接两个`nums`首尾相接, 这样跑一遍最长不重复子串就行. 用原始长度减掉最长子串长度就是要删去的最小长度. 然后WA了一(很多)发, 发现前面那样操作不是充要条件...
考虑这样一个`nums`: [1,1,1,2,3,4,5,6,6,6], 最长不重复子串在中间, 但是这样删掉首尾是删去了两段而题目要求一段. 

所以更新最长长度时要check: (`#`表示拼接点, 没有实际字符; 用字母表示的数字,不会影响题意)
- 左指针是否在`0`处:`[abcdef]ffffff#abcdefffffff`
- 右指针是否在`len(nums)-1`处: `aaaa[abcdef]#aaaaabcdef`
- 左右指针一个在左边一个在右边: `abccccc[cefg#ab]ccccccefg`
- 这三种以外的其他情况不可以更新最长子串长度

### 代码
```python
import collections
def do():
    n = int(input())
    nums = [int(i) for i in input().split(" ")]
    nums2 = nums + nums
    d = collections.defaultdict(int)
    l = 0
    max_unique = 1
    for r, i in enumerate(nums2):
        d[i] += 1
        while d[i] > 1:
            d[nums2[l]] -= 1
            l += 1
        if l == 0 or r == n-1 or (l < n and r >= n):
            max_unique = max(max_unique, r - l + 1)
    return n - max_unique
print(do())
```

## 1208C Magic Grid

### 描述
XOR版本的数独. 要求每行每列的XOR值是相同的. 输入矩阵边长n, 用`[0, n**2-1]`里面的数字填充出任意一个符合要求的矩阵.

### 思路
按照某种规律assign bit`0`或者`1`. 如果边长为2(这个题要求边长是4的倍数), 那么可以手算出一个结果:
```python
00, 01
10, 11
```
类似地, 如果边长是4, 可以把每个角落分割开, 然后扩增位数(divide and ~~conquer~~ fill):
```python
00**, 00**,  01**, 01**
00**, 00**,  01**, 01**
10**, 10**,  11**, 11**
10**, 10**,  11**, 11**
```
然后对于每个角落, 我们可以把(这个角落的)四组`**`分别替换成边长为2的那种情况, 例如右下角
```python
1100, 1101
1110, 1111
```
这样扩增每次行上和列上的`0`,`1`都是成倍增长. 只要边长为2时满足XOR相等,以后都会相等.

### 代码
```python
def do():
    n = int(input())
    res = [[0]*n for _ in range(n)]
    cur = 0
    for i in range(0, n, 2):
        for j in range(0, n, 2):
            for ii in range(2):
                for jj in range(2):
                    res[i + ii][j + jj] = cur
                    cur += 1
    for row in res:
        print(" ".join(map(str, row)))
do()
```


