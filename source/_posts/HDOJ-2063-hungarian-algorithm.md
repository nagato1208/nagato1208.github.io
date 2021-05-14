---
title: HDOJ-2063-hungarian-algorithm
date: 2019-09-13 00:46:36
categories: HDOJ
tags: ['graph', 'hungarian']
---

http://acm.hdu.edu.cn/showproblem.php?pid=2063
匈牙利算法模板题, 直接贴代码好了...

```python
# HDOJ 2063
import collections
def do():
    edges, girls, boys = map(int, input().split(" "))
    graph = collections.defaultdict(list)
    for _ in range(edges):
        girl, boy = map(int, input().split(" "))
        graph[girl - 1].append(boy - 1)

    match = [-1] * boys # assign girls for boys T_T

    def dfs(girl):
        for boy in graph[girl]:  # 枚举潜在的匹配对象
            if not seen[boy]:
                seen[boy] = 1
                # 本质是 改进匹配, res[boy]表示匹配对象, -1表示不存在
                if match[boy] == -1 or dfs(match[boy]):  # 如果这个男孩子的女朋友不存在, 或者他的女朋友可以接受别人(雾
                    match[boy] = girl
                    return True
        return False

    for girl in range(girls):
        seen = [0] * boys   # 每轮都要clear一下
        dfs(girl)
    print(match) # 分配方案
    return sum(girl != -1 for girl in match)  # 最大匹配

print(do())
```
