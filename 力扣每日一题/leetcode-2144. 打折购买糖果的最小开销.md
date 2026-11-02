# [2144. 打折购买糖果的最小开销](https://leetcode.cn/problems/minimum-cost-of-buying-candies-with-discount/)

###### 标签：贪心 数组

一家商店正在打折销售糖果。每购买 **两个** 糖果，商店会 **免费** 送一个糖果。

免费送的糖果唯一的限制是：它的价格需要小于等于购买的两个糖果价格的 **较小值** 。

- 比方说，总共有 `4` 个糖果，价格分别为 `1` ，`2` ，`3` 和 `4` ，一位顾客买了价格为 `2` 和 `3` 的糖果，那么他可以免费获得价格为 `1` 的糖果，但不能获得价格为 `4` 的糖果。

给你一个下标从 **0** 开始的整数数组 `cost` ，其中 `cost[i]` 表示第 `i` 个糖果的价格，请你返回获得 **所有** 糖果的 **最小** 总开销。

**示例 1：**

```
输入：cost = [1,2,3]
输出：5
解释：我们购买价格为 2 和 3 的糖果，然后免费获得价格为 1 的糖果。
总开销为 2 + 3 = 5 。这是开销最小的 唯一 方案。
注意，我们不能购买价格为 1 和 3 的糖果，并免费获得价格为 2 的糖果。
这是因为免费糖果的价格必须小于等于购买的 2 个糖果价格的较小值。
```

**示例 2：**

```
输入：cost = [6,5,7,9,2,2]
输出：23
解释：最小总开销购买糖果方案为：
- 购买价格为 9 和 7 的糖果
- 免费获得价格为 6 的糖果
- 购买价格为 5 和 2 的糖果
- 免费获得价格为 2 的最后一个糖果
因此，最小总开销为 9 + 7 + 5 + 2 = 23 。
```

**示例 3：**

```
输入：cost = [5,5]
输出：10
解释：由于只有 2 个糖果，我们需要将它们都购买，而且没有免费糖果。
所以总最小开销为 5 + 5 = 10 。
```

## 核心贪心思想

为了最小化总花费，我们要**尽可能让便宜的糖果免费，让贵的糖果付费**。
因此最优策略是：

1. 将糖果按价格**降序**排列（从最贵到最便宜）。
2. 每三个糖果分为一组（连续取），其中**最便宜的那个（每组第三个）免费**，前两个正常付费。
3. 最后不足三个的余数部分因为没有赠送，全部需要付费。

这样总花费 = 所有糖果价格之和 − 所有免费糖果的价格（即每组中最小的那个）。

```python
class Solution:
    def minimumCost(self, cost: List[int]) -> int:
        cost.sort()
        res = 0
        n = len(cost)
        for i in range(n-1,-1,-1):
            if (n-i)%3==0:
                continue
            res+=cost[i]
        return res

```



```python
class Solution:
    def minimumCost(self, cost: List[int]) -> int:
        cost.sort()
        res = 0
        if len(cost)<=2:
            return sum(cost)
        cost = cost[::-1]
        for i,value in enumerate(cost):
            if (i+1)%3==1:
                res+=value
            elif (i+1)%3==2:
                res+=value
            else:
                continue
        return res

```





```python
class Solution:
    def minimumCost(self, cost: List[int]) -> int:
        cost.sort()
        res = 0
        n = len(cost)
        remain =n%3
        for i in range(remain):
            res+=cost[i]
        for i in range(remain,n,3):
            res+=cost[i+1]+cost[i+2]
        return res

```

