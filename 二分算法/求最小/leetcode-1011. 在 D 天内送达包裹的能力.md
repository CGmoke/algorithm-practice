# [1011. 在 D 天内送达包裹的能力](https://leetcode.cn/problems/capacity-to-ship-packages-within-d-days/)

传送带上的包裹必须在 `days` 天内从一个港口运送到另一个港口。

传送带上的第 `i` 个包裹的重量为 `weights[i]`。每一天，我们都会按给出重量（`weights`）的顺序往传送带上装载包裹。我们装载的重量不会超过船的最大运载重量。

返回能在 `days` 天内将传送带上的所有包裹送达的船的最低运载能力。

**示例 1：**

```
输入：weights = [1,2,3,4,5,6,7,8,9,10], days = 5
输出：15
解释：
船舶最低载重 15 就能够在 5 天内送达所有包裹，如下所示：
第 1 天：1, 2, 3, 4, 5
第 2 天：6, 7
第 3 天：8
第 4 天：9
第 5 天：10

请注意，货物必须按照给定的顺序装运，因此使用载重能力为 14 的船舶并将包装分成 (2, 3, 4, 5), (1, 6, 7), (8), (9), (10) 是不允许的。 
```

**示例 2：**

```
输入：weights = [3,2,2,4,1,4], days = 3
输出：6
解释：
船舶最低载重 6 就能够在 3 天内送达所有包裹，如下所示：
第 1 天：3, 2
第 2 天：2, 4
第 3 天：1, 4
```

**示例 3：**

```
输入：weights = [1,2,3,1,1], days = 4
输出：3
解释：
第 1 天：1
第 2 天：2
第 3 天：3
第 4 天：1, 1
```

**提示：**

- `1 <= days <= weights.length <= 5 * 104`
- `1 <= weights[i] <= 500`



```python
class Solution:
    def shipWithinDays(self, weights: List[int], days: int) -> int:
        def check(mid):
            day=1
            tmp = mid
            for i in weights:
                if mid <i:
                    mid=tmp
                    day+=1
                mid-=i
            return day
        left =max(weights)-1
        right = sum(weights)
        while left+1<right:
            mid = (left+right)//2
            if check(mid)<=days:
                right=mid
            else:
                left = mid
        return right
```

## 总体思路

- 运力的最小值不能小于最重的包裹（否则根本装不下），最大值是全部包裹总重量（一天运完）。
- 在 `[max(weights)-1, sum(weights)]` 范围内二分查找**最小的可行运力**。
- 判定函数 `check(mid)` 模拟以运力 `mid` 运输所需的天数。
- 使用**开区间二分模板**，保证 `left` 永远不可行，`right` 永远可行，最终 `right` 就是答案。

## 代码逐段分析

### 1. `check(mid)` 判定函数

python

```
def check(mid):
    day = 1            # 当前已用天数，至少 1 天
    tmp = mid          # 保存初始运力，用于每天重置
    for i in weights:
        if mid < i:    # 剩余运力装不下当前包裹
            mid = tmp  # 开启新的一天，运力回满
            day += 1   # 天数 +1
        mid -= i       # 装载当前包裹，扣除运力
    return day
```



**逻辑细节**：

- `day = 1`：一开始就在第一天。
- 遍历每个包裹重量 `i`：
  - 如果当前剩余运力 `mid` 不够装 `i`（`mid < i`），则必须新的一天，天数 +1，运力重置为 `tmp`。
  - 然后在当天装载 `i`，`mid -= i`。
- 返回需要的总天数。

**重要注意**：

- 该实现有一个**隐含前提**：传入的 `mid` 必须 ≥ 每个包裹重量，否则会在 `mid < i` 时频繁重置，导致天数计算错误。
  例如 `mid=5`，包裹 `[10,1]`：第一天 `5<10` → 开启第二天（`day=2, mid=5`），然后 `5-10=-5`；遇到 `1`，`-5<1` 又开启第三天，天数完全错乱。
  **所以二分搜索的范围必须保证所有 `mid` ≥ `max(weights)`**。

------

### 2. 二分搜索初始化

python

```
left = max(weights) - 1   # 明确不可行的左边界
right = sum(weights)      # 明确可行的右边界（一天全运完）
```



- `left` 取 `max(weights) - 1`：这个运力连最重的单个包裹都运不了，**绝对不可行**。
- `right` 取 `sum(weights)`：一天全运完，显然**可行**（除非天数限制小于1，但题目 `days≥1`，所以肯定可行）。

这样维护了开区间 `(left, right)`：

- `left` 总是不可行
- `right` 总是可行
- 答案在 `right` 处收敛。

------

### 3. 二分循环

python

```
while left + 1 < right:
    mid = (left + right) // 2
    if check(mid) <= days:
        right = mid      # mid 可行，尝试更小的运力
    else:
        left = mid       # mid 不可行，增大运力
```



**模板解释**：

- 当 `left + 1 == right` 时循环终止，此时 `right` 就是最小可行值。
- `mid` 是中间值，调用 `check(mid)` 判断：
  - 如果所需天数 `≤ days`，说明 `mid` **可行**，将右边界收缩到 `mid`，寻找更小的可行值。
  - 否则 `mid` 不可行，将左边界扩大到 `mid`。

**为什么用 `left+1<right` 而非 `left<=right`？**
这是开区间的标准写法，避免死循环，且结束时不需要纠结是 `left` 还是 `right` 是答案，直接返回 `right`。

------

### 4. 返回结果

python

```
return right
```



循环结束时 `right` 就是满足条件的最小运力。

------

## 关键边界与细节讨论

### 为什么 `left` 不能是 0？

1. **`check` 函数未做防御**：如果 `mid < max(weights)`，`check` 会计算出严重高估的天数，破坏二分单调性，导致结果错误。
2. **开区间模板要求 `left` 不可行**：`max(weights)-1` 必定不可行；若 `left=0` 虽然是不可行，但搜索过程中 `mid` 可能落到 `< max(weights)` 的危险区间，引发上述错误。

### 为什么 `right = sum(weights)` 可行？

因为一天全运完必然满足任意 `days≥1` 的要求。

### 时间复杂度

- 模拟运输 `check()` 耗时 O(n)，n 为包裹数。
- 二分范围是 `[max(weights), sum(weights)]`，设其差为 S，二分次数 O(log S)。
- 总复杂度 O(n log S)，对于 n ≤ 5×10⁴，S ≤ 5×10⁷ 的题目规模足够。