---
title: codeforces-399B Red and Blue Balls
date: 2019-08-25 03:07:46
categories: Codeforces
tags: ['stack', 'math']
---
## 描述
原题在这: [https://codeforces.com/problemset/problem/399/B](https://codeforces.com/problemset/problem/399/B)
给一个用字符串表示的栈, `R`表示红球, `B`表示蓝球. 按照以下规则按顺序操作(循环), 直到不含`B`:
- 如果栈顶是`R`,持续pop
- 如果栈顶是`B`, 替换成`R`
- 如果栈不满, 补上`B`直到栈满

## 思路
乍一看是一道简单的implement的题, 于是单纯的implement一下, TLE.
观察(或者手算):
- 最终状态必然是`RRRRRR`这种.
- 如果当前栈顶连续的`B`只有一个, like `RRRRRB`, 那么本轮完成后是`RRRRRR`. 过渡到结束点用了`1`步.
- 如果当前栈顶是两个连续的`B`, like `RRRRBB`, 那么状态会是`RRRRBR` -> `RRRRRB` -> (第一种情况). 过渡到第一种情况用了`2`步.
- 如果当前栈顶是三个连续的`B`, like `RRRBBB`, 状态分别是`RRRBBR` -> `RRRBRB` -> `RRRBRR` -> `RRRRBB` -> (第二种情况). 过渡到第二种情况用了`4`步.
- 归纳可知对于末尾连续`n`个`B`, 会给结果带来`2^n - 1`的贡献.

## 代码
```python
def do():
    n = int(input())
    stack = [s for s in input()][::-1]
    b = stack.count("B")
    res = 0
    while b > 0:
        while stack and stack[-1] == "R":
            stack.pop()
        stack[-1] = "R"
        b -= 1
        if len(stack) < n:
            res += (1 << (n - len(stack))) - 1
            stack += ["R"] * (n - len(stack))
        res += 1
    return res
print(do())
```