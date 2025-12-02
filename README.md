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

