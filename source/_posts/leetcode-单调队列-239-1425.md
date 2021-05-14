---
title: 'leetcode-[单调队列]-[239,1425]'
categories: Leetcode
tags: ['dp', 'monotonous-queue']
---

## 239. Sliding Window Maximum

### 思路
类似这种***在某个valid value set***里面取最值的题目其实很多, 比如218 city skyline问题的heap解法, 也是把当前有效(可用)的高度维护在堆中,
每次取最值. 

同理, 本题我们用双端队列来维护当前所有的valid值. 每次检查队列内最早的元素坐标, 是否和当前坐标的距离大于k, 如果大于, (根据题意)需要扔掉(`q.popleft()`).
然后要把已经在队列里的, 并且值比当前值小的也扔掉(`q.pop()`), 因为当前的坐标的有效期(生命周期)肯定大于之前的, 所以如果当前值更大, 之前出现的比较小的值就没有存在于候选队列的必要了.
这样我们维护一个单调递减的队列, 每次可以`O(1)`复杂度从队首`q[0]`取最大值.

### 代码
```python
class Solution(object):
    def maxSlidingWindow(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: List[int]
        """
        q = collections.deque()
        res = []
        for i in range(len(nums)):
            if q and i - q[0] >= k:
                q.popleft()
            while q and nums[q[-1]] <= nums[i]:
                q.pop()
            q.append(i)
            if i >= k - 1:
                res.append(nums[q[0]])
        return res
```

无脑TreeMap:
```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        TreeMap<Integer, Integer> count = new TreeMap<>();
        int[] res = new int[nums.length - k + 1];
        for(int i=0; i<nums.length; i++){
            count.put(nums[i], count.getOrDefault(nums[i], 0) + 1);
            if(i >= k){
                count.put(nums[i - k], count.getOrDefault(nums[i - k], 0) - 1);
                if(count.get(nums[i - k]) == 0){
                    count.remove(nums[i - k]);
                }
            }
            if(i >= k - 1){
                res[i - k + 1] = count.lastKey();
            }
        }
        return res;
    }
}
```


## 1425. Constrained Subset Sum

### 思路
英文题目描述有点艰涩, 建议读中文描述. 我们要从一个数组中删除一些值(也可以不删), 要求删除操作之后的数组的相邻元素之间, 在原数组的距离不能大于k. 

换种理解方式: 我们要从一个数组中按顺序取值, 但是每次取值的跳跃不能太远, 比如这次取了坐标`i`处的值, 下次只能在`[i+1, i+k]`闭区间内取, 不能更远. 求这样取出来的值的最大和.

和上一题几乎一模一样, 包装了一层DP的思想. 用一个单调(递减)队列维护当前有效的坐标, 其中对应最大的和的坐标位置在队首. 每次检查队首要不要扔掉.  

### 代码
```python
class Solution(object):
    def constrainedSubsetSum(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        q = collections.deque()
        dp = nums[:] # dp[i]表示从i到右可以取到的最大的和
        for i in range(len(nums)-1, -1, -1):
            if q and q[0] - i > k: # 太远啦
                q.popleft()
            if q:
                dp[i] = max(dp[i], nums[i] + dp[q[0]])
            while q and dp[q[-1]] <= dp[i]:
                q.pop()
            q.append(i)
        return max(dp)
```

另外此题用Java的TreeMap/TreeSet也可解, 相当于TreeSet无脑替我们维护了最大值, 每次删除值的时候复杂度`O(logn)`. 

```java
class Solution {// From Cicindela
    public int constrainedSubsetSum(int[] nums, int k) {
        TreeMap<Integer, Integer> map = new TreeMap<>(); //key是dp[i]的值, value是出现了多少次,只有dp[i-k]出现了1次的时候,才可以删掉.
        map.put(0, 1);
        int[] dp = new int[nums.length];
        int res = Integer.MIN_VALUE;
        for(int i = 0; i < nums.length; i++){
            dp[i] = nums[i] + map.lastKey(); //max key in the TreeMap
            if(i >= k){
                map.put(dp[i-k], map.getOrDefault(dp[i-k], 0) - 1); // 减掉一次
                if(map.get(dp[i-k]) == 0){//是0的时候再删掉
                    map.remove(dp[i-k]);
                  }
            }
            map.put(dp[i], map.getOrDefault(dp[i], 0) + 1);
        }
        for(int value: dp){
            res = Math.max(res, value);
        }
        return res;   
    }
}
```


