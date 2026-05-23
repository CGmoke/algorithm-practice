# [2389. 和有限的最长子序列](https://leetcode.cn/problems/longest-subsequence-with-limited-sum/)

给你一个长度为 `n` 的整数数组 `nums` ，和一个长度为 `m` 的整数数组 `queries` 。

返回一个长度为 `m` 的数组 `answer` ，其中 `answer[i]` 是 `nums` 中 元素之和小于等于 `queries[i]` 的 **子序列** 的 **最大** 长度 。

**子序列** 是由一个数组删除某些元素（也可以不删除）但不改变剩余元素顺序得到的一个数组。

**示例 1：**

```
输入：nums = [4,5,2,1], queries = [3,10,21]
输出：[2,3,4]
解释：queries 对应的 answer 如下：
- 子序列 [2,1] 的和小于或等于 3 。可以证明满足题目要求的子序列的最大长度是 2 ，所以 answer[0] = 2 。
- 子序列 [4,5,1] 的和小于或等于 10 。可以证明满足题目要求的子序列的最大长度是 3 ，所以 answer[1] = 3 。
- 子序列 [4,5,2,1] 的和小于或等于 21 。可以证明满足题目要求的子序列的最大长度是 4 ，所以 answer[2] = 4 。
```

**示例 2：**

```
输入：nums = [2,3,4,5], queries = [1]
输出：[0]
解释：空子序列是唯一一个满足元素和小于或等于 1 的子序列，所以 answer[0] = 0 。
```

**提示：**

- `n == nums.length`
- `m == queries.length`
- `1 <= n, m <= 1000`
- `1 <= nums[i], queries[i] <= 106`

相关标签

###### 二分查找	前缀和	排序



```python
class Solution:
    def answerQueries(self, nums: List[int], queries: List[int]) -> List[int]:
        nums.sort()
        ans=[]
        for i in range(1,len(nums)):
            nums[i]+=nums[i-1]
        for i,query in enumerate(queries):
            ans.append(bisect.bisect_right(nums,query))
        return ans
```

## 代码逻辑分析

### 1. 排序

python

```
nums.sort()
```

- 要使得“挑选的元素和尽量小”以容纳更多元素，应优先选择最小的元素。
- 升序排序后，前缀和数组就能反映“选前 k 个最小元素”的累加和。

### 2. 原地构造前缀和

python

```
for i in range(1, len(nums)):
    nums[i] += nums[i-1]
```

- 依次将 `nums[i]` 变为 `nums[0] + ... + nums[i]`，即前 `i+1` 个元素的和。
- 原地修改，不需要额外空间。

### 二分查找处理查询

python

```python
for i, query in enumerate(queries):
    ans.append(bisect.bisect_right(nums, query))
```

- `bisect.bisect_right(nums, query)` 返回在**升序**数组 `nums`（现在已是前缀和）中，**第一个大于 `query` 的索引**，也就是满足 `sum ≤ query` 的最大元素个数 `k`。
  - 例如前缀和 `[1,3,6,10]`，`query=5`，`bisect_right` 返回 2（元素 6 的索引），即最多可以选 2 个（和=3 ≤ 5）。
- 这样每个查询的答案直接由二分查找给出。