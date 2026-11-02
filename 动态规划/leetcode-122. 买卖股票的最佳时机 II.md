# [122. 买卖股票的最佳时机 II](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/)

相关标签

[贪心](https://leetcode.cn/tag/greedy/)	[数组](https://leetcode.cn/tag/array/)	[动态规划](https://leetcode.cn/tag/dynamic-programming/)

给你一个整数数组 `prices` ，其中 `prices[i]` 表示某支股票第 `i` 天的价格。

在每一天，你可以决定是否购买和/或出售股票。你在任何时候 **最多** 只能持有 **一股** 股票。然而，你可以在 **同一天** 多次买卖该股票，但要确保你持有的股票不超过一股。

返回 *你能获得的 **最大** 利润* 。

**示例 1：**

```
输入：prices = [7,1,5,3,6,4]
输出：7
解释：在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5 - 1 = 4。
随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6 - 3 = 3。
最大总利润为 4 + 3 = 7 。
```

**示例 2：**

```
输入：prices = [1,2,3,4,5]
输出：4
解释：在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5 - 1 = 4。
最大总利润为 4 。
```

**示例 3：**

```
输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下, 交易无法获得正利润，所以不参与交易可以获得最大利润，最大利润为 0。
```

**提示：**

- `1 <= prices.length <= 3 * 10e4`
- `0 <= prices[i] <= 104`

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        n = len(prices)
        @cache
        def dfs(i,hold):
            if i<0:
                return -inf if hold else 0
            if hold:
                return max(dfs(i-1,True),dfs(i-1,False)-prices[i])
            return max(dfs(i-1,False),dfs(i-1,True)+prices[i])
        return dfs(n-1,False)
```

### 1. 状态定义

定义 `dfs(i, hold)`，表示：
**考虑从第 0 天到第 `i` 天结束，且当前是否持有股票 (`hold`) 时，能获得的最大利润。**

- `i`：当前考虑的天数索引（0-based）。
- `hold`：布尔值，表示第 `i` 天结束后手中是否持有股票。

最终答案要求**最后一天结束后不持有股票**的最大利润，即 `dfs(n-1, False)`。

------

### 2. 状态转移（决策）

每天结束后有两种状态：

- **当前持有股票（`hold=True`）**：
  可能是前一天就持有，今天什么都不做；或者是前一天没持有，今天**买入**股票（买入花费 `prices[i]`，所以利润要减去价格）。

  python

  ```
  dfs(i, True) = max( dfs(i-1, True),  dfs(i-1, False) - prices[i] )
  ```

  

- **当前不持有股票（`hold=False`）**：
  可能是前一天就不持有，今天什么都不做；或者是前一天持有，今天**卖出**股票（卖出获得 `prices[i]`，利润要加上价格）。

  python

  ```
  dfs(i, False) = max( dfs(i-1, False), dfs(i-1, True) + prices[i] )
  ```

  

------

### 3. 边界条件（递归出口）

当 `i < 0` 时，表示还没有开始任何交易日：

- 如果此时已经持有股票（`hold=True`），这不可能，因此返回**负无穷**（`-inf`），表示非法状态。
- 如果不持有股票（`hold=False`），初始利润为 0。