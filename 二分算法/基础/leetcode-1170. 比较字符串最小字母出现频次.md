# [1170. 比较字符串最小字母出现频次](https://leetcode.cn/problems/compare-strings-by-frequency-of-the-smallest-character/)

定义一个函数 `f(s)`，统计 `s` 中**（按字典序比较）最小字母的出现频次** ，其中 `s` 是一个非空字符串。

例如，若 `s = "dcce"`，那么 `f(s) = 2`，因为字典序最小字母是 `"c"`，它出现了 2 次。

现在，给你两个字符串数组待查表 `queries` 和词汇表 `words` 。对于每次查询 `queries[i]` ，需统计 `words` 中满足 `f(queries[i])` < `f(W)` 的 **词的数目** ，`W` 表示词汇表 `words` 中的每个词。

请你返回一个整数数组 `answer` 作为答案，其中每个 `answer[i]` 是第 `i` 次查询的结果。

**示例 1：**

```
输入：queries = ["cbd"], words = ["zaaaz"]
输出：[1]
解释：查询 f("cbd") = 1，而 f("zaaaz") = 3 所以 f("cbd") < f("zaaaz")。
```

**示例 2：**

```
输入：queries = ["bbb","cc"], words = ["a","aa","aaa","aaaa"]
输出：[1,2]
解释：第一个查询 f("bbb") < f("aaaa")，第二个查询 f("aaa") 和 f("aaaa") 都 > f("cc")。
```

**提示：**

- `1 <= queries.length <= 2000`
- `1 <= words.length <= 2000`
- `1 <= queries[i].length, words[i].length <= 10`
- `queries[i][j]`、`words[i][j]` 都由小写英文字母组成

```python
import bisect
queries = input().split()
words = input().split()
def f(s):
    min_char=min(s)
    return s.count(min_char)
f_words= [f(word) for word in words]
f_words.sort()
res=[]
for i in queries:
    left=len(words)-bisect.bisect_right(f_words,f(i))
    res.append(left)
print(res)
```

## 一、算法逻辑解析

### 1. 核心函数 `f(s)`

python

```
def f(s):
    min_char = min(s)
    return s.count(min_char)
```



- 功能：返回字符串 `s` 中**字典序最小字符的出现次数**。
- 举例：`f("bbb") = 3`，`f("aab") = 2`（最小字符是 `'a'`，出现2次）。

### 2. 预处理 `words`

python

```
f_words = [f(word) for word in words]
f_words.sort()
```



- 计算出 `words` 中每个单词的频次值，得到一个列表。
- 将列表**升序排序**，为二分查找做准备。

### 3. 对每个 `queries` 进行查询

python

```python
for i in queries:               # i 实际是查询字符串，建议命名 q
    left = len(words) - bisect.bisect_right(f_words, f(i))
    res.append(left)
```



- `bisect.bisect_right(f_words, f(i))`：返回在已排序的 `f_words` 中，**第一个严格大于 `f(i)` 的元素索引**。
  也就是 `f_words` 中 **≤ f(i) 的元素个数**。
- `len(words) - 该索引`：即**严格大于 `f(i)` 的元素个数**，恰好是题目所需的结果。

### 4. 算法本质

通过**排序 + 二分查找**，将每次查询的线性遍历 `O(m)` 降为对数时间 `O(log m)`，避免了对 `words` 的重复扫描。

------

## 二、复杂度分析

| 复杂度类型     | 具体推导                                                     |
| :------------- | :----------------------------------------------------------- |
| **时间复杂度** | 计算 `f_words`：所有单词的字符总长度为 `L_words`，`min(s)` 和 `count` 各需遍历字符串，故 `O(L_words)`；排序 `O(m log m)`（`m = len(words)`）；处理 `queries`：每个查询计算 `f(q)` 需 `O(len(q))`（总 `O(L_queries)`），二分查找 `O(n log m)`（`n = len(queries)`）。 **总体**：`O(L_words + L_queries + m log m + n log m)` |
| **空间复杂度** | `f_words` 列表占 `O(m)`，结果列表 `res` 占 `O(n)`，其他开销 `O(1)`，故 **`O(m + n)`**。 |

------

## 三、代码优点

1. **正确且高效**：利用 `bisect_right` 精准处理“严格大于”的条件，避免边界错误。
2. **简洁易读**：函数 `f` 封装了核心计算，主流程仅 6 行逻辑清晰。
3. **直接可用**：标准输入读取，输出结果列表，适合本地测试和竞赛环境。
4. 

## 扩展：为什么用 `bisect_right` 而不是 `bisect_left`？

- 若 `f_words = [1,2,2,3]`，`f_q = 2`，要统计 `>2` 的个数（即 `3`）。
- `bisect_right` 返回第一个 `> f_q` 的索引 → 索引 3，`len - 3 = 1` ✅
- `bisect_left` 返回第一个 `≥ f_q` 的索引 → 索引 1，`len - 1 = 3` ❌（会把等于的也算进去）

所以用 `bisect_right` 是最直接的。
