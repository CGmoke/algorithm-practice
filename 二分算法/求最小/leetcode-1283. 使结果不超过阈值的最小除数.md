# [1283. 使结果不超过阈值的最小除数](https://leetcode.cn/problems/find-the-smallest-divisor-given-a-threshold/)

给你一个整数数组 `nums` 和一个正整数 `threshold` ，你需要选择一个正整数作为除数，然后将数组里每个数都除以它，并对除法结果求和。

请你找出能够使上述结果小于等于阈值 `threshold` 的除数中 **最小** 的那个。

每个数除以除数后都向上取整，比方说 7/3 = 3 ， 10/2 = 5 。

题目保证一定有解。

**示例 1：**

```
输入：nums = [1,2,5,9], threshold = 6
输出：5
解释：如果除数为 1 ，我们可以得到和为 17 （1+2+5+9）。
如果除数为 4 ，我们可以得到和为 7 (1+1+2+3) 。如果除数为 5 ，和为 5 (1+1+1+2)。
```

**示例 2：**

```
输入：nums = [2,3,5,7,11], threshold = 11
输出：3
```

**示例 3：**

```
输入：nums = [19], threshold = 5
输出：4
```

**提示：**

- `1 <= nums.length <= 5 * 10^4`
- `1 <= nums[i] <= 10^6`
- `nums.length <= threshold <= 10^6`



```python
class Solution:
    def smallestDivisor(self, nums: List[int], threshold: int) -> int:
        left=0
        right=max(nums)
        while left+1<right:
            mid = (left+right)//2
            if sum((x-1)//mid +1 for x in nums)<=threshold:
                right = mid
            else:
                left = mid
        return right


```

### 表达式 `(x - 1) // mid`+1 的含义

题目要求每个元素 `x` 除以除数 `divisor` 后**向上取整**，即 `ceil(x / divisor)`。
常规写法是 `(x + divisor - 1) // divisor`。
这里使用的 `(x - 1) // mid + 1` 是另一种等价形式：

text

```
ceil(x / mid) = (x - 1) // mid + 1
```

**举例验证：**

- `x=5, mid=2`：`(5-1)//2 +1 = 4//2+1=2+1=3`，常规 `(5+1)//2=3` ✅
- `x=6, mid=2`：`(6-1)//2 +1 =5//2+1=2+1=3`，常规 `(6+1)//2=3` ✅
- `x=1, mid=3`：`(1-1)//3 +1 =0+1=1`，常规 `(1+2)//3=1` ✅

所以 `(x - 1) // mid` 实际上是 `ceil(x / mid) - 1`。
因此整个总和公式可以写成：

text

```
sum( ceil(x/mid) ) = sum( (x-1)//mid + 1 ) = sum((x-1)//mid) + len(nums)
```

### 为什么条件成立时执行 `right = mid`？

这是二分查找中**找最小可行解**的经典模式。我们需要的是一个**最小的满足条件的除数**。

- 当 `mid` 满足条件时，说明 `mid` 是一个**可行解**，但可能还有更小的除数也满足条件，因此我们缩小搜索范围的上界，向左半区继续查找：`right = mid`（保留 `mid` 作为候选）。
- 当 `mid` 不满足条件时，说明这个除数太小了（因为除数越小，除法结果之和越大），需要增大除数，于是移动下界：`left = mid`（放弃 `mid` 及其左侧）。

通过这种收缩，最终 `left` 和 `right` 会夹逼到边界，`right` 停在最小的可行解上。

**二分模板特点：**
代码使用 `while left + 1 < right` 保证循环结束时 `left + 1 == right`，此时 `right` 就是最小可行解（前提是搜索区间内存在解，且初始 `left` 为不可行端，`right` 为可行端）。这种模板要求区间是左开右闭 `(left, right]` 或左不可行右可行。