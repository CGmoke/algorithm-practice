# [3633. 最早完成陆地和水上游乐设施的时间 I](https://leetcode.cn/problems/earliest-finish-time-for-land-and-water-rides-i/)

难度：简单

给你两种类别的游乐园项目：**陆地游乐设施** 和 **水上游乐设施**。

- 陆地游乐设施
  - `landStartTime[i]` – 第 `i` 个陆地游乐设施最早可以开始的时间。
  - `landDuration[i]` – 第 `i` 个陆地游乐设施持续的时间。
- 水上游乐设施
  - `waterStartTime[j]` – 第 `j` 个水上游乐设施最早可以开始的时间。
  - `waterDuration[j]` – 第 `j` 个水上游乐设施持续的时间。

一位游客必须从 **每个** 类别中体验 **恰好****一个** 游乐设施，顺序 **不限** 。

- 游乐设施可以在其开放时间开始，或 **之后任意时间** 开始。
- 如果一个游乐设施在时间 `t` 开始，它将在时间 `t + duration` 结束。
- 完成一个游乐设施后，游客可以立即乘坐另一个（如果它已经开放），或者等待它开放。

返回游客完成这两个游乐设施的 **最早可能时间** 。

 

**示例 1:**

**输入：**landStartTime = [2,8], landDuration = [4,1], waterStartTime = [6], waterDuration = [3]

**输出：**9

**解释：**

- 方案 A（陆地游乐设施 0 → 水上游乐设施 0）：
  - 在时间 `landStartTime[0] = 2` 开始陆地游乐设施 0。在 `2 + landDuration[0] = 6` 结束。
  - 水上游乐设施 0 在时间 `waterStartTime[0] = 6` 开放。立即在时间 `6` 开始，在 `6 + waterDuration[0] = 9` 结束。
- 方案 B（水上游乐设施 0 → 陆地游乐设施 1）：
  - 在时间 `waterStartTime[0] = 6` 开始水上游乐设施 0。在 `6 + waterDuration[0] = 9` 结束。
  - 陆地游乐设施 1 在 `landStartTime[1] = 8` 开放。在时间 `9` 开始，在 `9 + landDuration[1] = 10` 结束。
- 方案 C（陆地游乐设施 1 → 水上游乐设施 0）：
  - 在时间 `landStartTime[1] = 8` 开始陆地游乐设施 1。在 `8 + landDuration[1] = 9` 结束。
  - 水上游乐设施 0 在 `waterStartTime[0] = 6` 开放。在时间 `9` 开始，在 `9 + waterDuration[0] = 12` 结束。
- 方案 D（水上游乐设施 0 → 陆地游乐设施 0）：
  - 在时间 `waterStartTime[0] = 6` 开始水上游乐设施 0。在 `6 + waterDuration[0] = 9` 结束。
  - 陆地游乐设施 0 在 `landStartTime[0] = 2` 开放。在时间 `9` 开始，在 `9 + landDuration[0] = 13` 结束。

方案 A 提供了最早的结束时间 9。

**示例 2:**

**输入：**landStartTime = [5], landDuration = [3], waterStartTime = [1], waterDuration = [10]

**输出：**14

**解释：**

- 方案 A（水上游乐设施 0 → 陆地游乐设施 0）：
  - 在时间 `waterStartTime[0] = 1` 开始水上游乐设施 0。在 `1 + waterDuration[0] = 11` 结束。
  - 陆地游乐设施 0 在 `landStartTime[0] = 5` 开放。立即在时间 `11` 开始，在 `11 + landDuration[0] = 14` 结束。
- 方案 B（陆地游乐设施 0 → 水上游乐设施 0）：
  - 在时间 `landStartTime[0] = 5` 开始陆地游乐设施 0。在 `5 + landDuration[0] = 8` 结束。
  - 水上游乐设施 0 在 `waterStartTime[0] = 1` 开放。立即在时间 `8` 开始，在 `8 + waterDuration[0] = 18` 结束。

方案 A 提供了最早的结束时间 14。****

 

**提示:**

- `1 <= n, m <= 100`
- `landStartTime.length == landDuration.length == n`
- `waterStartTime.length == waterDuration.length == m`
- `1 <= landStartTime[i], landDuration[i], waterStartTime[j], waterDuration[j] <= 1000`

```python
class Solution:
    def earliestFinishTime(self, landStartTime: List[int], landDuration: List[int], waterStartTime: List[int], waterDuration: List[int]) -> int:
        res = float('inf')
        for a,b in zip(landStartTime,landDuration):
            for c,d in zip(waterStartTime,waterDuration):
                res = min(res,max(a+b,c)+d)
                res = min(res,max(c+d,a)+b)
        return res
```

# 算法思想：暴力枚举 + 顺序抉择

## 核心逻辑

代码遍历所有 `(land, water)` 实例对，对每一对计算两种执行顺序的完成时间：

- **先执行 land 再执行 water**：
  - land 的开始时间 = `a`（`landStartTime`），结束时间 = `a + b`（`landDuration` 为 `b`）。
  - water 只能在 land 结束后才能开始，故实际开始时间 = `max(a + b, c)`，其中 `c` 为 `waterStartTime`。
  - 总完成时间 = `max(a + b, c) + d`（`d` 为 `waterDuration`）。
- **先执行 water 再执行 land**（对称）：
  - 完成时间 = `max(c + d, a) + b`。

取这两种顺序的较小值作为该实例对的局部最优，最后对所有实例对取全局最小值。

## 算法假设

- 两类任务之间**无依赖**，可以任意选择实例并按任意顺序串行执行。
- 同一类任务中**只能且必须选一个**实例。
- 任务不可抢占，不可重叠。

##  复杂度

- **时间复杂度**：O(L × W)，其中 L、W 分别是 `land` 和 `water` 的实例个数。若两者规模均为 n，则为 O(n²)。
- **空间复杂度**：O(1)，仅使用常数个变量。