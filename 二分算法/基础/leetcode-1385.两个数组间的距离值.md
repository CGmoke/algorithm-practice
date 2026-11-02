# 1385.[两个数组间的距离值](https://leetcode.cn/problems/find-the-distance-value-between-two-arrays/)

给你两个整数数组 `arr1` ， `arr2` 和一个整数 `d` ，请你返回两个数组之间的 **距离值** 。

「**距离值**」 定义为符合此距离要求的元素数目：对于元素 `arr1[i]` ，不存在任何元素 `arr2[j]` 满足 `|arr1[i]-arr2[j]| <= d` 。

**示例 1：**

```
输入：arr1 = [4,5,8], arr2 = [10,9,1,8], d = 2
输出：2
解释：
对于 arr1[0]=4 我们有：
|4-10|=6 > d=2 
|4-9|=5 > d=2 
|4-1|=3 > d=2 
|4-8|=4 > d=2 
所以 arr1[0]=4 符合距离要求

对于 arr1[1]=5 我们有：
|5-10|=5 > d=2 
|5-9|=4 > d=2 
|5-1|=4 > d=2 
|5-8|=3 > d=2
所以 arr1[1]=5 也符合距离要求

对于 arr1[2]=8 我们有：
|8-10|=2 <= d=2
|8-9|=1 <= d=2
|8-1|=7 > d=2
|8-8|=0 <= d=2
存在距离小于等于 2 的情况，不符合距离要求 

故而只有 arr1[0]=4 和 arr1[1]=5 两个符合距离要求，距离值为 2
```

**示例 2：**

```
输入：arr1 = [1,4,2,3], arr2 = [-4,-3,6,10,20,30], d = 3
输出：2
```

**示例 3：**

```
输入：arr1 = [2,1,100,3], arr2 = [-5,-2,10,-3,7], d = 6
输出：1
```

**提示：**

- `1 <= arr1.length, arr2.length <= 500`

- `-10^3 <= arr1[i], arr2[j] <= 10^3`

- `0 <= d <= 100`

  

```python
class Solution:
    def findTheDistanceValue(self, arr1: List[int], arr2: List[int], d: int) -> int:
        def lower_bound(nums,target):
            left =0
            right=len(nums)-1
            while left<=right:
                mid=(left+right)//2
                if nums[mid]>=target:
                    right=mid-1
                else:
                    left=mid+1
            return left
        arr2.sort()
        res =0
        for x in arr1:
            i=lower_bound(arr2,x-d)
            if i==len(arr2) or arr2[i]>x+d:
                res+=1
        return res
```

##### 注：判断arr2中的每个元素是否在[arr1-d,arr1+d]的范围内，如果不在则否和题目要求

### 1. 排序

```
arr2.sort()
```

- 先对 `arr2` 升序排序，目的是为了后续能用二分查找快速定位区间边界。
- 注意：代码里出现了两次 `arr2.sort()`，第二次是多余的，不影响正确性但无必要。

### 2. 二分查找辅助函数 `lower_bound`

```python
def lower_bound(nums, target):
    left = 0
    right = len(nums) - 1
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] >= target:
            right = mid - 1
        else:
            left = mid + 1
    return left
```



- 功能：返回 `nums` 中第一个 **大于等于** `target` 的元素的索引。
- 如果所有元素都小于 `target`，则返回 `len(nums)`。
- 这是经典的二分查找实现，复杂度 **O(log m)**，`m = len(arr2)`。

### 3. 遍历 `arr1` 进行判定

```python
res = 0
for x in arr1:
    i = lower_bound(arr2, x - d)
    if i == len(arr2) or arr2[i] > x + d:
        res += 1
```



对每个 `x`，进行如下判断：

- 查找 `arr2` 中第一个 **大于等于 `x - d`** 的元素位置 `i`。
- 如果 `i == len(arr2)`：
  - 说明 `arr2` 所有元素都 **小于** `x - d`。
  - 那么对任意 `y ∈ arr2`，`x - y ≥ x - (x - d) = d`，即距离 > d（严格大于 `d`？题目通常要求 `|x - y| > d`，这里差值为 `x - y > d`，满足），所以该 `x` 符合条件。
- 如果 `i < len(arr2)` 且 `arr2[i] > x + d`：
  - 第一个 `≥ x - d` 的元素已经 **大于** `x + d`。
  - 那么数组被分为两部分：
    - 索引 `< i` 的元素：都 `< x - d` → 距离 `x - y > d`。
    - 索引 `≥ i` 的元素：都 `≥ arr2[i] > x + d` → 距离 `y - x > d`。
  - 因此 `arr2` 中所有元素与 `x` 的距离均大于 `d`，符合条件。
- 否则（`i < len(arr2)` 且 `arr2[i] ≤ x + d`）：
  - 说明存在元素位于区间 `[x - d, x + d]` 内，距离 ≤ d，不符合条件。

所以通过一次二分查找就能判断该 `x` 是否合法，非常高效。

## 复杂度分析

- **时间复杂度**：`O(m log m + n log m)`
  - 排序 `arr2`：`O(m log m)`，`m = len(arr2)`
  - 遍历 `arr1`，每次二分查找：`O(n log m)`，`n = len(arr1)`
- **空间复杂度**：`O(1)`（忽略排序可能使用的栈空间）

## 代码中可改进的小细节

- 第2行和第11行对 `arr2` 重复排序，可以去掉一个。

- 二分查找 `lower_bound` 也可以直接用 Python 标准库 `bisect_left`，功能完全一致，代码更简洁：

  ```python
  from bisect import bisect_left
  i = bisect_left(arr2, x - d)
  ```