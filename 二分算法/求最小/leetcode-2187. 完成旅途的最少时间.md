# [2187. 完成旅途的最少时间](https://leetcode.cn/problems/minimum-time-to-complete-trips/)

给你一个数组 `time` ，其中 `time[i]` 表示第 `i` 辆公交车完成 **一趟****旅途** 所需要花费的时间。

每辆公交车可以 **连续** 完成多趟旅途，也就是说，一辆公交车当前旅途完成后，可以 **立马开始** 下一趟旅途。每辆公交车 **独立** 运行，也就是说可以同时有多辆公交车在运行且互不影响。

给你一个整数 `totalTrips` ，表示所有公交车 **总共** 需要完成的旅途数目。请你返回完成 **至少** `totalTrips` 趟旅途需要花费的 **最少** 时间。

 

**示例 1：**

```
输入：time = [1,2,3], totalTrips = 5
输出：3
解释：
- 时刻 t = 1 ，每辆公交车完成的旅途数分别为 [1,0,0] 。
  已完成的总旅途数为 1 + 0 + 0 = 1 。
- 时刻 t = 2 ，每辆公交车完成的旅途数分别为 [2,1,0] 。
  已完成的总旅途数为 2 + 1 + 0 = 3 。
- 时刻 t = 3 ，每辆公交车完成的旅途数分别为 [3,1,1] 。
  已完成的总旅途数为 3 + 1 + 1 = 5 。
所以总共完成至少 5 趟旅途的最少时间为 3 。
```

**示例 2：**

```
输入：time = [2], totalTrips = 1
输出：2
解释：
只有一辆公交车，它将在时刻 t = 2 完成第一趟旅途。
所以完成 1 趟旅途的最少时间为 2 。
```

 

**提示：**

- `1 <= time.length <= 105`
- `1 <= time[i], totalTrips <= 107`

##  思路

时间越多，可以完成的旅途也就越多；时间越少，可以完成的旅途也就越少。有这样的性质，我们可以二分猜答案。关于二分的原理，请看视频【基础算法精讲 04】。

猜测答案为 x，问题变成：

每辆车都用时 x，总共能完成多少趟旅途？能否达到 totalTrips？
根据题意，我们可以完成
$$
\sum_{i=0}^{n-1} \left\lfloor \frac{x }{ \text{time}[i] } \right\rfloor
$$
趟旅途，将它与 totalTrips 比较，如果比 totalTrips 小，说明二分的答案小了，更新二分区间左端点 left，否则更新二分区间右端点 right。

## 细节

下面代码采用开区间二分，这仅仅是二分的一种写法，使用闭区间或者半闭半开区间都是可以的。

开区间左端点初始值：min(time)−1。这个时间任何车都没法完成一趟旅途，一定不满足题目要求。
开区间右端点初始值：让最快的车完成 totalTrips 趟旅途，用时
$$
min(time)⋅totalTrips
$$
，这个时间一定能满足题目要求。
优化（可选）：开区间左端点初始值：假设所有 time[i] 都等于 min(time)，那么每辆车可以完成的旅途为
$$
k=\frac{x}{\min(\text{time})}。需要满足 k⋅n≥totalTrips，即k \ge \left\lceil \frac{\text{totalTrips}}{n} \right\rceil。
$$
取:
$$
x = \min(\text{time}) \cdot \left\lceil \frac{\text{totalTrips}}{n} \right\rceil - 1
$$
，这个时间一定不满足题目要求。
开区间右端点初始值：假设所有 time[i] 都等于 max(time)，那么每辆车可以完成的旅途为 
$$
k=\frac{x}{\max(\text{time})} 
$$
需要满足
$$
k⋅n≥totalTrips
$$
即 
$$
k \ge \left\lceil \frac{\text{totalTrips}}{n} \right\rceil
$$
取
$$
x = \max(\text{time}) \cdot \left\lceil \frac{\text{totalTrips}}{n} \right\rceil
$$
这个时间一定能满足题目要求。进一步地，可以与上面的 
$$
min(time)⋅totalTrips
$$
 取最小值，作为开区间右端点初始值。

```python
class Solution:
    def minimumTime(self, time: List[int], totalTrips: int) -> int:
        min_t = min(time)
        left = min_t - 1  # 循环不变量：sum >= totalTrips 恒为 False
        right = min_t * totalTrips  # 循环不变量：sum >= totalTrips 恒为 True
        while left + 1 < right:  # 开区间 (left, right) 不为空
            mid = (left + right) // 2
            if sum(mid // t for t in time) >= totalTrips:
                right = mid  # 缩小二分区间为 (left, mid)
            else:
                left = mid  # 缩小二分区间为 (mid, right)
        # 此时 left 等于 right-1
        # sum(left) < totalTrips 且 sum(right) >= totalTrips，所以答案是 right
        return right
```

复杂度分析
时间复杂度：O(nlogU)，其中 n 为 time 的长度，U 为二分上下界之差。
空间复杂度：O(1)。
