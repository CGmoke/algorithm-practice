# [275. H 指数 II](https://leetcode.cn/problems/h-index-ii/)

给你一个整数数组 `citations` ，其中 `citations[i]` 表示研究者的第 `i` 篇论文被引用的次数，`citations` 已经按照 **非降序排列** 。计算并返回该研究者的 h 指数。

[h 指数的定义](https://baike.baidu.com/item/h-index/3991452?fr=aladdin)：h 代表“高引用次数”（high citations），一名科研人员的 `h` 指数是指他（她）的 （`n` 篇论文中）**至少** 有 `h` 篇论文分别被引用了**至少** `h` 次。

请你设计并实现对数时间复杂度的算法解决此问题。

 

**示例 1：**

```
输入：citations = [0,1,3,5,6]
输出：3
解释：给定数组表示研究者总共有 5 篇论文，每篇论文相应的被引用了 0, 1, 3, 5, 6 次。
     由于研究者有3篇论文每篇 至少 被引用了 3 次，其余两篇论文每篇被引用 不多于 3 次，所以她的 h 指数是 3 。
```

**示例 2：**

```
输入：citations = [1,2,100]
输出：2
```

**提示：**

- `n == citations.length`
- `1 <= n <= 105`
- `0 <= citations[i] <= 1000`
- `citations` 按 **升序排列**

## 题解：

**题意**：找最大的 *h*，满足 *citations*[*n*−*h*]≥*h*。

<img src="E:\Documents\Typora\1698492370-tnIhCf-lc275-c.png" alt="lc275-c.png" style="zoom: 33%;" />

**注意**：我们二分的是答案，是**引用次数**，不是 *citations* 的下标。

#### 闭区间写法

```python
class Solution:
    def hIndex(self, citations: List[int]) -> int:
        # 在区间 [left, right] 内询问
        left = 1
        right = len(citations)
        while left <= right:  # 区间不为空
            # 循环不变量：
            # left-1 的回答一定为「是」
            # right+1 的回答一定为「否」
            mid = (left + right) // 2
            # 引用次数最多的 mid 篇论文，引用次数均 >= mid
            if citations[-mid] >= mid:
                left = mid + 1  # 询问范围缩小到 [mid+1, right]
            else:
                right = mid - 1  # 询问范围缩小到 [left, mid-1]
        # 循环结束后 right 等于 left-1，回答一定为「是」
        # 根据循环不变量，right 现在是最大的回答为「是」的数
        return right
```

#### 左闭右开区间写法

```python
class Solution:
    def hIndex(self, citations: List[int]) -> int:
        # 在区间 [left, right) 内询问
        left = 1
        right = len(citations) + 1
        while left < right:  # 区间不为空
            # 循环不变量：
            # left-1 的回答一定为「是」
            # right 的回答一定为「否」
            mid = (left + right) // 2
            # 引用次数最多的 mid 篇论文，引用次数均 >= mid
            if citations[-mid] >= mid:
                left = mid + 1  # 询问范围缩小到 [mid+1, right)
            else:
                right = mid  # 询问范围缩小到 [left, mid)
        # 根据循环不变量，left-1 现在是最大的回答为「是」的数
        return left - 1
```

#### 左开右闭区间写法

```python
class Solution:
    def hIndex(self, citations: List[int]) -> int:
        # 在区间 (left, right] 内询问
        left = 0
        right = len(citations)
        while left < right:  # 区间不为空
            # 循环不变量：
            # left 的回答一定为「是」
            # right+1 的回答一定为「否」
            mid = (left + right + 1) // 2  # 保证 mid 在二分区间内
            # 引用次数最多的 mid 篇论文，引用次数均 >= mid
            if citations[-mid] >= mid:
                left = mid  # 询问范围缩小到 (mid, right]
            else:
                right = mid - 1  # 询问范围缩小到 (left, mid-1]
        # 根据循环不变量，left 现在是最大的回答为「是」的数
        return left
```

#### 开区间写法

```python
class Solution:
    def hIndex(self, citations: List[int]) -> int:
        # 在区间 (left, right) 内询问
        left = 0
        right = len(citations) + 1
        while left + 1 < right:  # 区间不为空
            # 循环不变量：
            # left 的回答一定为「是」
            # right 的回答一定为「否」
            mid = (left + right) // 2
            # 引用次数最多的 mid 篇论文，引用次数均 >= mid
            if citations[-mid] >= mid:
                left = mid  # 询问范围缩小到 (mid, right)
            else:
                right = mid  # 询问范围缩小到 (left, mid)
        # 根据循环不变量，left 现在是最大的回答为「是」的数
        return left
```

#### 复杂度分析

时间复杂度：O(logn)，其中 n 为 citations 的长度。每次循环，都会将二分范围减少一半，所以循环会执行 O(logn) 次，所以时间复杂度为 O(logn)。
空间复杂度：O(1)。仅用到若干额外变量。

