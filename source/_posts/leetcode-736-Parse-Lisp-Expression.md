---
title: leetcode-736 Parse Lisp Expression
date: 2019-08-25 03:02:17
categories: Leetcode
tags: ['stack', 'parser']
---
## 描述
题目本身比较长, 大意是parse一个lisp like的语言. 可以从样例中get到信息:

- `(add 1 2)` 返回1+2 => 3.
- `(mult 3 (add 2 3))` 先计算2+3, 返回的5再和3相乘 => 15.
- `(let x 2 (mult x 5))` `x`被赋值2, 然后`x`和5相乘等于10, 然后返回10. 注意`let`语句如果变量个数是奇数个, 那么最后一个是返回值.
- `(let x 3 x 2 x)` `x`先被赋值3, 再被赋值2, 最后返回`x`的值2.
- `(let x 1 y 2 x (add x y) (add x y))` 在计算内层的`(add x y)`时用到了之前的赋值语句. 
- `(let x 1 y (let x 2 x) x)` 首先`x`被赋值1, 然后内层的`(let x 2 x)`给`x`赋值2并返回, 然后`y`被赋值2. 最后`x`的值应该是本层的值,与更深层的`x`无关.

## 思路
这个题的重点感觉不仅仅是`stack`本身, 更多在于函数作用域的理解.

首先我们需要用一个字典`token`来存变量和值. 例如`{x:12, y:34}`.

通过观察可以得知以下几点: 
- 运算符只有三种`{add, mult, let}`, 其中`add`和`mult`只有两个操作数. `let`可以有`>=1`个操作数,但是我们可以每次两两处理来赋值, 最后return剩余的那个.
- 每次`let`的开始, 都会进入一个新的作用域. 外层的变量仍然有效, 并允许**暂时**覆盖外层的变量.
- 每次遇到右括号`)`意味着可以开始计算本层语句的值, 对于`add`和`mult`来说计算完直接返回就行. 不影响变量`token`本身. 对于`let`来说,计算完返回上一层时,需要重置`token`.
- 只有`let`语句有权更新`token`. 并且每次`let`后面积攒了两个操作数的时候, 需要及时更新`token`, 否则进入新的作用域时, 可能会出现需要retrieve某个变量值但是找不到的情况. 例如`(let x 2 (mult x 5))`. 

所以我们用一个`stack`来存当前读入的所有操作符和操作数(变量&值). 用一个`token_stack`来存每个`let`语句对应的当前`token`. 遇到新的`let`时把当前`token`入栈, 遇到`)`并pop出某个内层`let`时,之前旧版本的`token`出栈.


## 代码
```python
class Solution(object):
    def evaluate(self, expression):
        """
        :type expression: str
        :rtype: int
        """
        expression = "(let x " + expression + " x)"
        stack = []
        token_stack = [{}]
        
        def check():
            count = 0
            for s in stack[::-1]:
                if s in op_dict:
                    if s == "let":
                        return count
                    return -1
                count += 1
            return -1
        
        def getval(x):
            return token[x] if x in token else x
            
        cur = ""
        token = {}
        op_dict = {"let", "add", "mult"}
            
        for i,c in enumerate(expression):
            if c == "(":
                continue                    
            elif c == ")":
                data = []
                while stack and stack[-1] not in op_dict:
                    data.append(stack.pop())
                data = data[::-1]
                op = stack.pop()
                if op == "let":
                    token_stack.pop()
                    if len(data) > 1:
                        token[data[0]] = getval(data[1])
                    else:
                        stack.append(getval(data[0]))
                else:
                    a = getval(data[0])
                    b = getval(data[1])
                    if op == "add":
                        stack.append(str(int(a) + int(b)))
                    else:
                        stack.append(str(int(a) * int(b)))
                token = dict(token_stack[-1])
            else:
                if c != " ":
                    cur += c
                    if i + 1 < len(expression) and expression[i+1] in "( )":
                        if cur in op_dict:
                            stack.append(cur)
                            if cur == "let":
                                token_stack.append(dict(token))
                        else:
                            stack.append(cur)
                        cur = ""
            if check() == 2:
                b = stack.pop()
                a = stack.pop() # assign value reversely
                token[a] = getval(b)
                token_stack[-1] = dict(token)
                        
        return int(stack[0])
```
