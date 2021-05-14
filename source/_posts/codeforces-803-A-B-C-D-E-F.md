---
title: codeforces-803-A-B-C-D-E-F总结
date: 2019-08-30 22:27:01
categories: Codeforces
tags: ['bfs', 'math', 'binary search', 'construct', 'number theory', 'dp']
---
2019年8月30日CF群打卡题: 803A, 803B, 803C, 803D. 附加题: 803E, 803F.

## 803A Maximal Binary Matrix

### 描述
大意是给一个`n`*`n`的矩阵, 初始全是`0`, 然后要求填充`k`个`1`, 并且填充完后的矩阵字典序最大(一行一行flatten之后的字典序) + 矩阵按对角线对称

### 思路
优先给第一行赋值, 因为要对称, 同时也给第一列赋值. 如果第一行完成后`k`没有用完, 那么继续第二列. 需要注意的是, 如果当前剩余的`k`是`1`, 那么无法继续对称地赋值(除了顶点都是成对的), 此时需要另起一行(给下一个顶点).

### 代码
```python
def do():
    n, k = map(int, input().split(" "))
    if k > n**2:
        print("-1")
        return
    res = [[0] * n for _ in range(n)]
    row = 0
    i = j = 0
    while i < n and k > 0:
        res[i][j] = 1
        res[j][i] = 1
        if i == j:
            k -= 1
        else:
            k -= 2
        i += 1
        if i == n or k == 1:
            row += 1
            i = j = row
 
    for line in res:
        print(" ".join(map(str, line)))

do()
```



## 803B Distances to Zero

### 描述
给一个数组`nums`, 为每个数找离它最近(左或者右)的`0`的距离. BFS水题, 一开始把所有`0`的位置入队就行.
```python
import collections
def do():
    N = int(input())
    nums = [int(i) for i in input().split(" ")]
    q = collections.deque()
    seen = [0] * N
    res = [float('inf')] * N
    for i,n in enumerate(nums):
        if n == 0:
            q.append((i, 0))
            seen[i] = 1
    while q:
        cur, dis = q.popleft()
        res[cur] = min(res[cur], dis)
        for nx in [cur-1, cur+1]:
            if 0<=nx<N and not seen[nx]:
                q.append((nx, dis+1))
                seen[nx] = 1
    print(" ".join(map(str, res)))
 
do()
```


## 803C Maximal GCD
### 描述
给一个正整数`n`, 要求输出一个长度为`k`的严格递增数组, 这个数组满足: 
1. 所有元素之和是`n`.
2. 所有元素的最大公约数`(gcd)`最大.

如果找不到满足条件的数组, 返回`-1`.

### 思路
假设目标数组所有元素最大公约数是`gcd`, 因为元素之和是`n`, `gcd`必然也是`n`的约数. 从`gcd`最小的倍数(它本身)开始考虑, 如果能找到一个长度为`k`单调递增的数列之和是`n`那么满足条件. 

所以问题转换为: 找到一个最大的`gcd`使得`gcd + gcd*2 + gcd*3 + ... + gcd*k <= n`. 如果和小于`n`, 我们可以调整最后一项的大小(增大最后一项, 反正不会重复). 但是如果和大于`n`, 因为这个和已经是我们能找到的最小的等差数列和了(对于长度为`k`而言), 必然这个`gcd`无解.

### 代码
```python
import math
def do():
    n, k = map(int, input().split(" "))
    gcd = -1
    for i in range(1, int(math.sqrt(n)) + 1):
        if n % i == 0:
            cp = n // i
            if i * k * (k + 1) // 2 <= n:
                gcd = max(gcd, i)
            if cp * k * (k + 1) // 2 <= n:
                gcd = max(gcd, cp)
                
    if gcd == -1:
        print("-1")
    else:
        res = [gcd*i for i in range(1, k)]
        pre = sum(res) if res else 0
        res.append(n - pre)
        print(" ".join(map(str, res)))
do()
```


## 803D Magazine Ad

### 描述
结合了字符串处理和二分搜索的题. 给一个字符串`ad`, 和一个我们能容忍的最大行数`k`. 现在要给这个字符串分行, 当在容忍范围内时, 求一个最小的行宽(单行长度).
分行规则:
- 空格处可以分行
- 字符`-`处可以分行. 分出的本行带有这个`-`
- 其他情况不可以分行

### 思路
二分一下所有的行宽. 满足条件就继续缩小, 否则增大. 不过这个题不好写的是判断函数... 需要双指针记录当前的字符和上一个分割处的字符`-`或者空格.

### 代码
```python
def do():
    k = int(input())
    ad = input()
 
    def valid(width, limit):
        l = -1
        count = 0
        cur = 0
        for r in range(len(ad)):
            if ad[r] == " " or ad[r] == "-":
                l = r
            cur += 1
            if cur == width:
                if l == -1 and r != len(ad) - 1:
                    return False
                count += 1
                if r == len(ad) - 1:
                    cur = 0
                else:
                    cur = r - l
                l = -1
                if count > limit:
                    return False
        if cur: count += 1
        return count <= limit
 
    lo, hi = 1, len(ad) + 1
    while lo < hi:
        mi = (lo + hi) >> 1
        if not valid(mi, k):
            lo = mi + 1
        else:
            hi = mi
    return lo
 
print(do())
```


## 803E Roma and Poker

### 描述
原题在讲故事...总结成自己的语言大概是给一个字符串`s`, 里面只有`WDL?`四种字符. `W`表示加1, `L`表示减1, `D`表示什么也不做, `?`表示这里可以是`WDL`的任意一种.
初始值为0. 另一个输入值是`k`. 要求重建字符串`s`(也就是把所有不确定的`?`确定化), 满足最终结果是`k`或者`-k`, 并且在字符串的中间任意一处结果不能是`k`或者`-k`.

### 思路
用一个`dp`字典, `dp[i]`表示在`i`点可以收集到的所有(中间)值. 每次结合当前字符和`dp[i-1]`确定此处的可能值. 如果`k`和`-k`没有出现在最后一个位置, 返回`NO`. 否则可以反向重建(对于`?`,根据右边的值,和当前的值推断`?`代表什么).
(这个题也挺水的其实...还不如C题难)

### 代码
```python
import collections
def do():
    n, k = map(int, input().split(" "))
    s = input()
    dp = collections.defaultdict(set)
    gap = {'W': 1, 'L': -1, 'D': 0}
    dp[-1].add(0)
    for i in range(n-1):
        if s[i] == '?':
            for pre in dp[i-1]:
                for g in gap.values():
                    t = pre + g
                    if abs(t) != k:
                        dp[i].add(t)
        else:
            for pre in dp[i-1]:
                t = pre + gap[s[i]]
                if abs(t) != k:
                    dp[i].add(t)
    if s[n-1] == '?':
        for pre in dp[n-2]:
            for g in gap.values():
                dp[n-1].add(pre + g)
    else:
        for pre in dp[n-2]:
            dp[n-1].add(pre + gap[s[n-1]])
    # print(dp)
    if k not in dp[n-1] and -k not in dp[n-1]:
        return "NO"
    res = [c for c in s]
    cur = k if k in dp[n-1] else -k
    for i in range(n-1, 0, -1):
        if s[i] == '?':
            for c in gap:
                if abs(cur - gap[c]) != k and cur - gap[c] in dp[i-1]:
                    res[i] = c
                    cur -= gap[c]
                    break
        else:
            cur -= gap[s[i]]
    if res[0] == '?':
        if cur == 0:
            res[0] = 'D'
        elif cur == 1:
            res[0] = 'W'
        else:
            res[0] = 'L'
    return "".join(res)
 
print(do())
```
 


## 803F Coprime Subsequences

### 描述
给一个数组, 问这个数组里面, 有多少个序列`subsequence`(下标不一样就算不同序列)满足: 这个序列的所有元素互质.

### 思路
所有元素互质意味这些元素的`gcd`是`1`. 我们可以枚举所有的因数, 每个因数对应着很多序列. 然后在总的序列数`2**n - 1`(长度`n`的序列有`2**n - 1`个子序列)中减掉因数不是`1`的那些.

上面的方法存在重复计数的问题: 对于因数`2`的所有序列中, 包含了因数`4`的. 所以对于`2`的序列来说, 要减去`4`, `6`, `8`...的数量. [容斥原理](https://zh.wikipedia.org/wiki/%E6%8E%92%E5%AE%B9%E5%8E%9F%E7%90%86).

具体操作时需要逆序, 这样对于嵌套的重复部分, 不会多次减去. 
### 代码
```python
import math
import collections
def do():
    n = int(input())
    nums = map(int, input().split(" "))
    count = collections.defaultdict(int)
    for num in nums:
        for i in range(1, int(math.sqrt(num))+1):
            cp = num // i
            if num % i == 0:
                count[i] += 1
            if cp != i and num % cp == 0:
                count[cp] += 1
    maxk = max(count.keys())
    freq = {k: (1 << count[k]) - 1 for k in count}
    for k in sorted(count.keys(), reverse=True):
        for kk in range(k << 1, maxk+1, k):
            freq[k] -= freq[kk] if kk in freq else 0
    return freq[1] % (10**9 + 7)
 
print(do())
```
