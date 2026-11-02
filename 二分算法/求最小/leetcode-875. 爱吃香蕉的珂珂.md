# [875. 爱吃香蕉的珂珂](https://leetcode.cn/problems/koko-eating-bananas/)

珂珂喜欢吃香蕉。这里有 `n` 堆香蕉，第 `i` 堆中有 `piles[i]` 根香蕉。警卫已经离开了，将在 `h` 小时后回来。

珂珂可以决定她吃香蕉的速度 `k` （单位：根/小时）。每个小时，她将会选择一堆香蕉，从中吃掉 `k` 根。如果这堆香蕉少于 `k` 根，她将吃掉这堆的所有香蕉，然后这一小时内不会再吃更多的香蕉。 

珂珂喜欢慢慢吃，但仍然想在警卫回来前吃掉所有的香蕉。

返回她可以在 `h` 小时内吃掉所有香蕉的最小速度 `k`（`k` 为整数）。

**示例 1：**

```
输入：piles = [3,6,7,11], h = 8
输出：4
```

**示例 2：**

```
输入：piles = [30,11,23,4,20], h = 5
输出：30
```

**示例 3：**

```
输入：piles = [30,11,23,4,20], h = 6
输出：23
```

**提示：**

- `1 <= piles.length <= 104`
- `piles.length <= h <= 109`
- `1 <= piles[i] <= 109`

```python
class Solution:
    def minEatingSpeed(self, piles: List[int], h: int) -> int:
        def check(k):
            time=0
            for i in piles:
                time+=(i+k-1)//k #当香蕉数量大于k时向上取整
            return time
        left = 0
        right = max(piles)
        while left+1<right:
            mid= (left+right)//2
            if check(mid)<=h:
                right=mid # 当前速度可行，尝试更小速度
            else:
                left = mid	# 速度太慢，需要提高速度
        return right
class Solution:
    def minEatingSpeed(self, piles: List[int], h: int) -> int:
        n = len(piles)
        left = 0  # 恒为 False
        right = max(piles)  # 恒为 True
        while left + 1 < right:  # 开区间不为空
            mid = (left + right) // 2
            if sum((p - 1) // mid for p in piles) <= h - n:
                right = mid  # 循环不变量：恒为 True
            else:
                left = mid  # 循环不变量：恒为 False
        return right  # 最小的 True
```

### 灵神解析：

看示例 1，piles=[3,6,7,11], h=8。

如果珂珂能用 k=4 的速度吃掉所有香蕉，那么也能用更快的速度 k=5,6,⋯ 吃掉所有香蕉。
如果珂珂不能用 k=3 的速度吃掉所有香蕉，那么也不能用更慢的速度 k=2,1,⋯ 吃掉所有香蕉。
这种单调性意味着我们像 二分查找 那样，把答案 k 猜出来。

要解决的问题变成：

- **判断**珂珂能否用 *k* 的速度，在 *h* 小时内吃掉所有香蕉。

假设一堆香蕉有 *p*=*piles*[*i*] 根，那么吃完这堆香蕉需要
$$
\left\lceil \frac{p}{k} \right\rceil = \left\lfloor \frac{p-1}{k} \right\rfloor + 1
$$
小时。上式的证明见 [上取整下取整转换公式的证明](https://leetcode.cn/link/?target=https%3A%2F%2Fzhuanlan.zhihu.com%2Fp%2F1890356682149838951)。

如果满足
$$
\sum_{i=0}^{n-1} \left( \left\lfloor \frac{piles[i]-1}{k} \right\rfloor + 1 \right) \le h
$$
即：
$$
n + \sum_{i=0}^{n-1} \left\lfloor \frac{piles[i]-1}{k} \right\rfloor \le h
$$
就说明珂珂可以用 k 的速度，在 h 小时内吃掉所有香蕉。

最后，确定二分的范围：

k=0 必然无法吃掉所有香蕉，作为（开区间的）左边界。
k=max(piles) 意味着 1 小时一定能吃完一堆香蕉，必然可以在 h 小时内吃掉所有香蕉（注意题目保证 n≤h），作为（开区间的）右边界。

### 代码逐段解析



```python
class Solution:
    def minEatingSpeed(self, piles: List[int], h: int) -> int:
        def check(k):
            time = 0
            for i in piles:
                time += (i + k - 1) // k   # 向上取整计算每堆所需小时
            return time
```



- **`check(k)`**：给定速度 `k`，计算吃完所有香蕉需要的总小时数。
  - `(i + k - 1) // k` 是整数向上取整的写法，等价于 `ceil(i / k)`。
  - 累加每堆所需时间，返回总耗时。

```python
        left = 0
        right = max(piles)
```



- **二分查找的左右边界**：
  - `left` 初始为 0，`right` 初始为最大堆的香蕉数（此时每小时吃一堆的最大速度）。
  - 理论上速度最小为 1，但 `left=0` 不会导致除零错误（后面解释），且能简化边界处理。
  - 搜索区间为 `(left, right]`，最终答案落在 `right`。

```python
        while left + 1 < right:
            mid = (left + right) // 2
            if check(mid) <= h:
                right = mid       # 当前速度可行，尝试更小速度
            else:
                left = mid        # 速度太慢，需要提高速度
```



- **二分循环条件**：`left + 1 < right` 确保区间内至少有两个数时继续。
- **中间值 `mid`**：整数除法，向下取整。
- **判断**：
  - 若 `check(mid) <= h`，说明速度 `mid` 能在 `h` 小时内完成，尝试更小速度 → 移动 `right` 到 `mid`。
  - 否则速度不足，必须提高 → 移动 `left` 到 `mid`。
- **循环不变量**：`left` 始终是不可行的速度（或 0），`right` 始终是可行的速度。

```python
        return right
```



- 循环结束时 `left + 1 == right`，`right` 就是最小的可行速度。

### 边界情况分析

| 情况                               | 示例                                                         | 过程                                                         |
| :--------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| `h` 足够大（如 `h >= sum(piles)`） | `piles=[3,6,7,11], h=100`                                    | 最小速度应为 1。`right=max=11`，二分最终 `right` 会收敛到 1。 |
| `h` 刚好等于堆数                   | `piles=[3,6,7,11], h=4`                                      | 必须每小时整堆吃完，最小速度 = 最大堆 = 11。二分初始 `right=11`，`check(11)=4`，`right` 不断左移但不会小于 11。 |
| `left=0` 的安全性问题              | 当 `right=1` 时，`left+1<right` 不成立，直接返回 `right=1`，不会调用 `check(0)`。当 `right>1` 时，`mid=(0+right)//2 ≥1`（因为整数除法），所以永远不会计算 `k=0`。因此安全。 |                                                              |

### 时间复杂度

- `check(k)` 遍历所有堆，时间复杂度 O(n)。
- 二分查找范围从 1 到 `max(piles)`，迭代次数 O(log M)，其中 M 为最大堆的香蕉数。
- 总复杂度 **O(n log M)**，空间复杂度 O(1)。



