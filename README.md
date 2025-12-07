# My algorithm journey

## 一、算法规范

### 1.1 二分

模板 1：**查找目标值，若不存在返回插入位置（lower_bound 风格）**

```cpp
int binarySearch(vector<int>& arr, int target) {
    int left = 0, right = arr.size(); // 注意：right 是开区间
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }
    return left; // 第一个 >= target 的位置
}
```

> 这个版本使用 **左闭右开** 区间 `[left, right)`，循环条件是 `left < right`，更不容易出错，且天然返回插入位置。

模板 2：**经典闭区间写法**

```cpp
int binarySearch(vector<int>& arr, int target) {
    int left = 0, right = arr.size() - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] == target) {
            return mid; // 找到目标
        } else if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return left; // 未找到，返回插入位置
}
```

> 两种都对，但 **模板 1 更适合处理“边界问题”**（比如找第一个 ≥ target 的位置），而模板 2 更直观。

模板1

- 更自然地处理“插入位置”或“边界查找”（如 `lower_bound`）。
- 终止时 `left` 指向第一个 **≥ target** 的位置（即使 `target` 不存在）。
- 无需额外检查 `mid` 是否等于 `target`，适合泛化搜索（如找上下界）。

模板2

- 更直观，直接判断 `arr[mid] == target` 并返回。
- 若未找到，返回的 `left` 是第一个 **> target** 的位置（需注意此时 `left` 可能越界）。
- 适合精确查找，但扩展性稍弱（如找上下界需额外逻辑）。

返回第一个 `>= target` 的pos，[0,nums.size()]

```cpp
int lower_bound(const vector<int>& nums, int target) {
        int left = 0, right = nums.size(); // 注意：right 是开区间
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }
```

### 1.2 栈

`string(1, s[ptr++])` 是什么？

这是 **`std::string` 的构造函数**的一种用法，具体是

```cpp
string(size_t n, char c);
```

- **功能**：构造一个包含 `n` 个相同字符 `c` 的字符串。
- 所以string(1, s[ptr++])表示：
  - 创建一个长度为 1 的字符串，
  - 内容是 `s[ptr]`（然后 `ptr` 自增）。

其他常见构造方式对比：

| 写法                  | 含义                                  |
| --------------------- | ------------------------------------- |
| `string("hello")`     | 从 C 风格字符串构造                   |
| `string(5, 'x')`      | `"xxxxx"`                             |
| `string(s, pos, len)` | 从 `s` 的 `pos` 开始取 `len` 个字符   |
| `string(1, c)`        | 将 `char c` 转为 `string`（常用技巧） |

------

类似 `isdigit(int ch)` 的函数有哪些？**（C/C++ 字符分类函数）**

这些函数都定义在 **`<cctype>`**（C++）或 **`<ctype.h>`**（C）中，**全部接受 `int` 参数，返回 `int`（非零表示 true）**。

> ⚠️ 使用时注意：**传入前最好转为 `unsigned char`**，避免负值未定义行为。

| 函数          | 功能                               | 等价判断（ASCII）                                     |
| ------------- | ---------------------------------- | ----------------------------------------------------- |
| `isalnum(c)`  | 字母或数字                         | `(c >= 'A' && c <= 'Z')                               |
| `isalpha(c)`  | 字母（大小写）                     | `(c >= 'A' && c <= 'Z')                               |
| `islower(c)`  | 小写字母                           | `c >= 'a' && c <= 'z'`                                |
| `isupper(c)`  | 大写字母                           | `c >= 'A' && c <= 'Z'`                                |
| `isdigit(c)`  | 十进制数字                         | `c >= '0' && c <= '9'`                                |
| `isxdigit(c)` | 十六进制数字（0-9, a-f, A-F）      | `'0'~'9'`, `'a'~'f'`, `'A'~'F'`                       |
| `isspace(c)`  | 空白字符                           | `' '`（空格）、`'\t'`、`'\n'`、`'\v'`、`'\f'`、`'\r'` |
| `ispunct(c)`  | 标点符号（非字母、非数字、非空白） | 如 `!@#$%^&*()` 等                                    |
| `isgraph(c)`  | 可打印字符（不含空格）             | `isalnum(c)                                           |
| `isprint(c)`  | 可打印字符（含空格）               | `isgraph(c)                                           |
| `iscntrl(c)`  | 控制字符（如 `\n`, `\t`, `\0` 等） | ASCII 0~31 和 127 (`DEL`)                             |

------

**转换函数（也来自 `<cctype>`）**

| 函数         | 功能                   |
| ------------ | ---------------------- |
| `tolower(c)` | 转小写（若为大写字母） |
| `toupper(c)` | 转大写（若为小写字母） |

> 注意：这些函数**不会修改原字符**，而是**返回转换后的值**。

### 1.3 单调栈

hot100_793  hot100_84

**栈顶最小单调栈**

左侧到右侧遍历，某个元素的下一个更大值。

```cpp
for(int i=0;i<n;i++){
    while(!s.empty()&&temperatures[i]>temperatures[s.top()]){
        int tmp=s.top();
        ans[tmp]=i-tmp;
        s.pop();
    }
    s.push(i);
}
```

**栈顶最大单调栈**

左侧到右侧遍历，某个元素的下一个更小值。

```cpp
for(int i=0;i<n;i++){
    while(!mono_stack.empty()&&heights[mono_stack.top()]>=heights[i]){
        mono_stack.pop();
    }
    left[i]=(mono_stack.empty()?-1:mono_stack.top());
    mono_stack.push(i);
}
```

核心：要求第一个更小值还是第一个更大值。

更大值就需要栈顶最小单调栈，遍历时候 当前元素大于栈顶，那么栈顶出栈，记录坐标，小于栈顶就push栈里。

更小值就需要栈顶最小栈，遍历时候 当前元素小于栈顶，那么出栈，记录坐标，大于栈顶就push栈里。

### 1.3 回溯

代表题目：

**N 皇后问题**：在 N×N 棋盘上放置 N 个皇后，使其互不攻击。

**全排列 / 子集 / 组合生成**：如 LeetCode 中的 “Permutations”、“Subsets”、“Combination Sum” 等。

**数独求解**：填充空格使得每行、每列、每个 3×3 子格都包含 1~9。

**单词搜索（Word Search）**：在二维字符网格中查找是否存在某个单词。

**图的着色问题**：用最少颜色给图着色，相邻节点颜色不同。



全排列问题思考方式：

1. 写一个朴素 DFS 框架
2. 问自己：
   - 我要生成什么？→ 所有排列
   - 一个排列怎么构成？→ 一个个选数字，直到选满 `n` 个
   - 那递归函数的任务是什么？→ “当前已经选了一些数，继续往下选”
3. dfs骨架

```cpp
void dfs(...) {
    if (已经选够了) {
        把当前结果存起来；
        return;
    }
    for (每个可能的数字) {
        选它；
        dfs(继续选)；
        // 先不管撤销...
    }
}
```

4. 明确「状态」需要哪些变量

   - **现在已经选了哪些数？** → 用 `path`（vector）

   - **哪些数还能选？** → 最简单方式：用 `used` 数组标记是否用过

5. 不能重复选

   在 `for` 循环里：

   - 遍历所有 `nums[i]`
   - 如果 `used[i] == true`，跳过（不能重复选）

6. 加上「回溯」——撤销操作！

```cpp
path.pop_back();
used[i] = false;
```



## 二、特征题总结

### 2.1 柱子题

hot100_84

问题化解：找出一维数组中，某个元素的左侧或者右侧最近小于这个值的坐标
