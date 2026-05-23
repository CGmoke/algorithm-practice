# [17. 电话号码的字母组合](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。答案可以按 **任意顺序** 返回。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

<img src="E:\Documents\Typora\1752723054-mfIHZs-image.png" alt="img" style="zoom:25%;" />

**示例 1：**

```
输入：digits = "23"
输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

**示例 2：**

```
输入：digits = "2"
输出：["a","b","c"]
```

 **提示：**

- `1 <= digits.length <= 4`
- `digits[i]` 是范围 `['2', '9']` 的一个数字。



## 1.代码：

```python
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        data = ['','','abc','def','ghi','jkl','mno','pqrs','tuv','wxyz']
        n = len(digits)
        if n ==0:
            return []
        res = []
        path = ['']*n
        def dfs(i):
            if i==n:
                res.append(''.join(path))
                return 
            for c in data[int(digits[i])]:
                path[i] = c
                # print(path)
                dfs(i+1)
        dfs(0)
        return res
```



```python
digits = input()
nums = ['','','abc','def','ghi','jkl','mno','pqrs','tuv','wxyz']
n = len(digits)
if n ==0:
    print( [])
res = []
path = ['']*n
def dfs(i):
    if i==n:
        res.append(''.join(path))
        return
    for c in nums[int(digits[i])]:
        path[i] = c
        # print(path)
        dfs(i+1)
dfs(0)
print( res)
```

## 2. 边界条件与初始化

python

```
n = len(digits)
if n == 0:
    return []
res = []
path = [''] * n
```



- `n` 记录输入数字串的长度，也代表最终每个字母组合的长度。
- **空输入**：根据题意，空字符串应返回空列表 `[]`，而不是包含一个空字符串的列表。
- `res` 用于收集所有完整路径（即最终组成的字母组合）。
- `path` 是一个**固定长度的字符数组**，长度为 `n`。在递归过程中，`path[i]` 会依次填充第 `i` 个数字对应的某个字母。之所以用列表而不是字符串拼接，是因为列表元素可以原地修改，省去反复创建新字符串的开销。

##  3.深度优先搜索（DFS）递归函数

python

```python
def dfs(i):
    if i == n:
        res.append(''.join(path))
        return 
    for c in data[int(digits[i])]:
        path[i] = c
        dfs(i+1)
```



这段 DFS 是整个算法的核心，状态变量 `i` 表示当前正在处理数字串中的第 `i` 位（从 0 开始）。

###  3.1终止条件（递归基）

python

```python
if i == n:
    res.append(''.join(path))
    return
```



- 当 `i == n` 时，说明已经处理完全部数字，`path` 数组中正好填满了长度为 `n` 的字符序列。
- 此时用 `''.join(path)` 将字符数组拼成一个字符串，加入到结果列表 `res` 中。
- 随后 `return` 回溯到上一层。

###  3.2候选扩展与递归

python

```python
for c in data[int(digits[i])]:
    path[i] = c
    dfs(i+1)
```



- 计算当前数字字符对应的整数值：`int(digits[i])`，再到 `data` 中取出该数字对应的字母串（如 `'abc'`）。
- 遍历这个字母串中的每个字符 `c`：
  - 将 `path[i]` 赋值为 `c`，表示第 `i` 位选择该字母。
  - 递归进入下一层 `dfs(i+1)`，去决定下一位数字的字母。
- **关键点**：这里并没有显式地“撤销” `path[i]` 的值，因为下一次循环会把 `path[i]` 覆盖为新的字符。这就是常说的**隐式回溯**——由于使用的是数组的索引赋值，而非 `append/pop`，旧值会被新值自然覆盖，不影响后续搜索。

##  4.搜索过程举例

以输入 `digits = "23"` 为例：

1. `data[2] = 'abc'`，`data[3] = 'def'`。
2. `path = ['', '']`，`res = []`。
3. 启动 `dfs(0)`：
   - i=0，数字 '2' → `'abc'`。
     - c='a'：`path[0]='a'` → `dfs(1)`
       - i=1，数字 '3' → `'def'`。
         - c='d'：`path[1]='d'` → `dfs(2)`， i==2，`res.append("ad")` 并返回。
         - c='e'：`path[1]='e'` → `dfs(2)` → `res.append("ae")`。
         - c='f'：`path[1]='f'` → `res.append("af")`。
     - c='b'：`path[0]='b'` → `dfs(1)` … 得到 "bd","be","bf"。
     - c='c'：`path[0]='c'` → `dfs(1)` … 得到 "cd","ce","cf"。
4. 最终 `res = ["ad","ae","af","bd","be","bf","cd","ce","cf"]`。

## 5.回溯的细节说明

在典型的回溯模板里，我们常看到：

python

```
path.append(c)
dfs(...)
path.pop()
```



而这里使用的是**索引直接赋值 + 定长数组**：

- 因为 `path` 的长度在初始化时就固定为 `n`，且每一层递归访问的索引 `i` 是固定的。
- 递归进入下一层时只会修改 `path[i+1]`，不影响当前层的 `path[i]`。
- 从下一层返回后，本层循环走到下一个字符，直接覆盖 `path[i]` 即可，无需“恢复现场”。

因此，**虽然没有显式 pop，但回溯的逻辑依然成立**。这是一种空间更优的实现方式，避免了频繁的列表追加和弹出操作，也避免了递归中产生大量临时列表。

## 6. 时间复杂度

设输入数字串长度为 `n`，每个数字最多对应 4 个字母（7 和 9 对应 4 个，其余为 3 个）。

- 最坏情况下，每位数字都有 4 种选择，组合总数为 O(4n)*O*(4*n*)。
- 每生成一个组合，都需要 `''.join(path)` 花费 O(n)*O*(*n*) 的时间拼接字符串。
- 总时间复杂度：O(n⋅4n)*O*(*n*⋅4*n*)。

## 7.空间复杂度

- 输出结果存储了 O(4n)*O*(4*n*) 个字符串，每个字符串长度 n*n*，输出空间 O(n⋅4n)*O*(*n*⋅4*n*)，但这通常不计入额外空间复杂度（取决于分析惯例）。
- **额外空间**（递归栈与临时变量）：
  - `path` 数组长度为 n*n*，O(n)*O*(*n*)。
  - 递归深度为 n*n*，所以递归调用栈占 O(n)*O*(*n*)。
- 因此，**额外空间复杂度为 O(n)\*O\*(\*n\*)**（若不计输出结果）。

## 8.可能的改进与变体

- **使用字典映射**：可以用字典 `{'2':'abc', '3':'def', ...}` 代替列表，语义更清晰，但列表凭借整型索引访问更直接。
- **使用 `itertools.product`**：由于本题本质是多个集合的笛卡尔积，完全可以用 `product(*[data[int(d)] for d in digits])` 直接生成，但手写 DFS 是面试中展示递归/回溯能力的基础。
- **提前剪枝**：本题没有剪枝需求，因为是生成全部组合。