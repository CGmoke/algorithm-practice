# [33. 搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

整数数组 `nums` 按升序排列，数组中的值 **互不相同** 。

在传递给函数之前，`nums` 在预先未知的某个下标 `k`（`0 <= k < nums.length`）上进行了 **向左旋转**，使数组变为 `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]`（下标 **从 0 开始** 计数）。例如， `[0,1,2,4,5,6,7]` 下标 `3` 上向左旋转后可能变为 `[4,5,6,7,0,1,2]` 。

给你 **旋转后** 的数组 `nums` 和一个整数 `target` ，如果 `nums` 中存在这个目标值 `target` ，则返回它的下标，否则返回 `-1` 。

你必须设计一个时间复杂度为 `O(log n)` 的算法解决此问题。

**示例 1：**

```
输入：nums = [4,5,6,7,0,1,2], target = 0
输出：4
```

**示例 2：**

```
输入：nums = [4,5,6,7,0,1,2], target = 3
输出：-1
```

**示例 3：**

```
输入：nums = [1], target = 0
输出：-1
```

**提示：**

- `1 <= nums.length <= 5000`

- `-104 <= nums[i] <= 104`

- `nums` 中的每个值都 **独一无二**

- 题目数据保证 `nums` 在预先未知的某个下标上进行了旋转

- `-104 <= target <= 104`

  由于该题要求算法时间复杂度为 `O(log n)`所以 根据题目所要求的结果有限考虑二分算法

```python
#左闭右开类型
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        n = len(nums)
        left = 0
        right = n
        while left<right:
            mid = (left+right)//2
            if nums[mid] == target:
                return mid
            if nums[left]<nums[mid]:
                if target<nums[mid] and nums[left]<=target:
                    right = mid
                else:
                    left = mid+1
            else:
                if nums[mid]<target and target<=nums[right-1]:
                    left = mid+1
                else:
                    right = mid
        return -1
        
```

```python
#闭区间类型
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        n = len(nums)
		left, right = 0, n - 1
        while left <= right:
            mid = (left + right) // 2
            if nums[mid] == target:
                return mid
            if nums[left] <= nums[mid]:
                if nums[left] <= target < nums[mid]:
                        right = mid - 1
                else:
                        left = mid + 1
            else:
                if nums[mid] < target <= nums[right]:
                    left = mid + 1
                else:
                    right = mid - 1
        return -1
```



## 算法思想

### 1. 问题特点

给定一个**互不相同**的整数数组 `nums`，它原本是升序排列的，但在某个未知下标 `k` 进行了旋转，例如 `[0,1,2,3,4,5,6]` 旋转后可能变成 `[4,5,6,0,1,2,3]`。
要在这样的数组中寻找 `target`，要求 **O(log n)** 时间。

### 2. 核心思路：每次确定一半有序，再判断 target 在哪一半

虽然整个数组不是完全有序的，但**任意二分后，至少有一半是有序的**。
我们可以利用这一半有序的部分来判断 `target` 是否落在其中，从而排除另一半。

**具体步骤（区间左闭右开 `[left, right)`，初始 `left=0, right=n`）**：

1. 计算 `mid = (left + right) // 2`，如果 `nums[mid] == target` 直接返回 `mid`。
2. **判断哪一半是有序的**：
   - 如果 `nums[left] < nums[mid]`：说明 **左半部分 `[left, mid)` 是有序的**（因为旋转后有序段一定是递增的，且左侧起点小于中间，中间不可能落在旋转的下坡处）。
   - 否则，**右半部分 `(mid, right-1]` 是有序的**（包含 `mid` 等于 `left` 的特殊情况，此时右半部分就是剩下的一个元素，自然有序）。
3. **在有序的那一半中，判断 `target` 是否在范围内**：
   - 如果左半有序且 `nums[left] <= target < nums[mid]`，说明 `target` 肯定在左半部分，将搜索区间缩小为左半 `right = mid`。
   - 如果左半有序但 `target` 不在上述范围，则它只可能在右半部分，于是 `left = mid + 1`。
   - 如果右半有序且 `nums[mid] < target <= nums[right-1]`，说明 `target` 在右半部分，`left = mid + 1`。
   - 如果右半有序但 `target` 不在该范围，则它必在左半部分，`right = mid`。
4. 重复直到区间为空（`left == right`），未找到则返回 `-1`。

### 3. 举例验证

```
nums = [4,5,6,7,0,1,2], target = 0
```

- `left=0, right=7, mid=3 (7)`
  `nums[left]=4 < nums[mid]=7` → 左半 `[0,3]` 有序 `[4,5,6,7]`。
  `target=0` 不在 `[4,7)` 范围 → `left=4`
- `left=4, right=7, mid=5 (1)`
  `nums[left]=0 < nums[mid]=1` → 左半 `[4,5]` 有序 `[0,1]`。
  `target=0` 在 `[0,1)` 内 → `right=5`
- `left=4, right=5, mid=4 (0)` → 找到，返回 `4`。

### 4. 边界条件与细节

- `right` 初始化为 `n`（开区间），所以循环条件是 `left < right`，访问最后一个元素时用 `nums[right-1]` 才能正确包含。
- 当 `mid == left` 时（区间只剩两个元素），`nums[left] < nums[mid]` 不成立，进入 `else` 分支，此时右侧只有一个元素，被视作有序，处理正确。
- 严格使用 `<` 和 `<=` 控制开闭区间，不会漏掉元素或死循环。

### 5. 复杂度分析

- **时间复杂度**：O(log n)，每次迭代将搜索范围缩小一半。
- **空间复杂度**：O(1)，只用了常数变量。