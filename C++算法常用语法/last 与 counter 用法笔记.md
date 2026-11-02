
两者都是基于 `std::unordered_map` 的用法模式，区别在于 **value 存什么**。
## 一、核心区别

|           | `counter`（频次表） | `last` / `lastIdx`（最近下标表） |
| --------- | -------------- | ------------------------- |
| **key**   | 元素值            | 元素值                       |
| **value** | 出现**次数**       | **最近一次**出现的下标             |
| **更新方式**  | `m[x]++`（累加）   | `m[x] = i`（覆盖）            |
| **回答的问题** | "出现了几次？"       | "上次在哪？"                   |
| **典型题**   | 统计频次、找众数、判断重复  | 判断两相同元素**距离**是否 ≤ k       |

## 二、`counter`（频次统计）

### 声明与构建

```cpp
#include <unordered_map>
unordered_map<int, int> counter;
for (int x : nums) counter[x]++;   // 不存在时自动为 0 再 +1
```

### 常用操作

```cpp
counter[x]++;                       // 计数 +1
if (counter.count(x)) { ... }       // 是否存在
if (counter[x] > 1) { ... }         // 出现超过 1 次（重复）
int c = counter.at(x);              // 安全取值（不存在抛异常）
```

### 典型场景

- 判断数组中是否有重复元素：`counter> 1`
- 找出现次数最多的值
- 异位词分组、字符频率统计

### 代码模板

```cpp
bool hasDuplicate(vector<int>& nums) {
    unordered_map<int, int> counter;
    for (int x : nums) {
        if (counter[x]++ > 0) return true;  // 之前已出现过
    }
    return false;
}
```

## 三、`last` / `lastIdx`（最近下标）

### 声明与构建

```cpp
#include <unordered_map>
unordered_map<int, int> lastIdx;    // 值 -> 最近下标
for (int i = 0; i < nums.size(); i++) {
    lastIdx[nums[i]] = i;           // 覆盖为最新位置
}
```

### 常用操作

```cpp
auto it = lastIdx.find(x);          // 查上次位置（推荐，不误插入）
if (it != lastIdx.end()) {
    int prev = it->second;          // 上次下标
    int dist = i - prev;            // 距离
}
lastIdx[x] = i;                     // 更新为最新下标
```

### 典型场景

- **`containsNearbyDuplicate`**：`i - lastIdx<= k`
- 判断两相同元素距离是否在 k 内
- 滑动窗口类「记住上次位置」的问题

### 代码模板

```cpp
bool containsNearbyDuplicate(vector<int>& nums, int k) {
    unordered_map<int, int> lastIdx;
    for (int i = 0; i < nums.size(); i++) {
        auto it = lastIdx.find(nums[i]);
        if (it != lastIdx.end() && i - it->second <= k)
            return true;
        lastIdx[nums[i]] = i;        // 覆盖更新
    }
    return false;
}
```

## 四、通用坑与技巧

| 坑 | 说明 | 正确做法 |
| --- | --- | --- |
| **`[]` 误插入** | `m[x]` 在 x 不存在时会插入 `{x, 0}` | 只读用 `find` / `count` / `at` |
| **覆盖 vs 累加** | `last` 用 `=`，`counter` 用 `++`，别混 | 想清楚 value 语义 |
| **更新时机** | `last` 要在判断距离**之后**再更新 | 先比较 `i - prev`，再 `= i` |
| **有序需求** | 需要按 key 排序遍历 | 改用 `std::map` |

## 五、如何选择

```
需要知道"次数"        → counter   (m[x]++)
需要知道"上次在哪"    → lastIdx   (m[x] = i)
需要判断"距离 ≤ k"    → lastIdx
需要判断"是否重复"    → counter 或 lastIdx 都行
```

## 六、一句话记忆

- **`counter` = 累加次数**，回答「几次」
- **`last` = 覆盖下标**，回答「上次在哪」
- 两者底层都是 `unordered_map`，只是 value 语义不同；只读时用 `find`，别用 `[]` 误插入。