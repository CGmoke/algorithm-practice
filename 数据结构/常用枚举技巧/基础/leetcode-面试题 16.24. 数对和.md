# [面试题 16.24. 数对和](https://leetcode.cn/problems/pairs-with-sum-lcci/)

中等

设计一个算法，找出数组中两数之和为指定值的所有整数对。一个数只能属于一个数对。

**示例 1：**

```
输入：nums = [5,6,5], target = 11
输出：[[5,6]]
```

**示例 2：**

```
输入：nums = [5,6,5,6], target = 11
输出：[[5,6],[5,6]]
```

**提示：**

- `nums.length <= 100000`
- `-105 <= nums[i], target <= 105`

```python
class Solution:
    def pairSums(self, nums: List[int], target: int) -> List[List[int]]:
        res = []
        cnt = Counter(nums)
        for i in nums:
            j = target-i
            if j>i:
                continue
            if cnt[j]>0 and cnt[i]>0 :
                if i==j:
                    if cnt[i]>=2:
                        cnt[i]-=2
                        res.append([i,j])
                else:
                    cnt[i]-=1
                    cnt[j]-=1
                    res.append([i,j])
        return res
```

算法思想：

先用 `Counter` 统计每个数字出现的次数 `cnt`，然后遍历每个数字 `i`，它的补数是 `j = target - i`。

关键点是「每个数只处理一次、每个数对只统计一次」：

- 只考虑 `i >= j` 的情况（即 `j > i` 时直接 `continue`）。因为数对是无序的，`(i, j)` 和 `(j, i)` 是同一对，固定让较大的数来做主，就能避免同一个数对被重复枚举。
- 当 `i != j` 时，只要 `cnt[i]` 和 `cnt[j]` 都还有剩余，就消耗掉一个 `i` 和一个 `j`（两者计数各减 1），并记录数对 `[i, j]`。
- 当 `i == j`（即 `target` 为偶数、`i = target / 2`）时，需要两个相同的数才能凑成一对，所以要判断 `cnt[i] >= 2`，满足时一次减掉 2 并记录 `[i, i]`。

由于计数是实时扣减的，已经配对过的数字不会被再次使用，天然满足题目「一个数只能属于一个数对」的约束。用哈希计数比排序后双指针更直观，也无需关心数对在数组中的先后顺序。

#### 复杂度分析

- 时间复杂度：O(*n*)，其中 *n* 为 *nums* 的长度，统计频率和遍历各一次，哈希表单次操作均摊 O(1)。
- 空间复杂度：O(*n*)，哈希表最多存储 *n* 个不同数字的计数（另需 O(*n*) 存放结果数对）。
