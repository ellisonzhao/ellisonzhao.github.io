# 二分查找总结


# 二分查找总结

二分查找是一种在 **有序数组** 中查找某一特定元素的搜索算法。元素集合有顺序，元素性质有分界点，二分法就可以用来求分界点，并不一定要求集合中元素是不重复的。

算法思路：假设目标值在闭区间 `[left, right]` 中， 每次将区间长度缩小一半，当 `left = right` 时，我们就找到了目标值。

## 常规写法

二分查找需要注意 **查找区间** 和 **终止条件**，稍不留神可能出现死循环。常见的写法如下：

```C++
int binarySearch(vector<int> &nums, int target) {
  int left = 0, right = nums.size() - 1; // 定义 target 在左闭右闭的区间里，[left, right]
  while (left <= right) { // 当 left==right，区间 [left, right] 依然有效，所以用 <=
    int mid = left + (right - left) / 2); // 防止溢出，结果等同于(left + right)/2
    if (nums[mid] > target) {
      right = mid - 1; // target 在左区间，所以更新为 [left, mid - 1]
    } else if (nums[mid] < target) {
      left = mid + 1; // target 在右区间，所以更新为 [mid + 1, right]
    } else {
      // nums[mid] == target
      return mid; // 数组中找到目标值，直接返回下标
    }
  }
  // 未找到目标值
  return -1;
}
```

上述写法中区间 `[left, right]` 的更新操作是 `right = mid - 1; left = mid + 1;` 

边界条件需要注意：二分区间直到长度为 `1`，即 `left == right` 时，循环条件依然满足，再进行一次比较，所以用 `left <= right`

例题：

[704. 二分查找](https://leetcode-cn.com/problems/binary-search/) 

对上述写法稍作改造，总结二分模板共有两个：

## 模板一

当区间 `[left, right]` 的更新操作是 `left = mid + 1; right = mid;` 时，二分区间计算 `mid` 不需要加 `1`。

通用模板写法：

```C++
int binarySearch(int left, int right) {
  while (left < right) {
    int mid = (left + right) / 2; // 注意防止溢出
    if (check(mid)) // 判断 mid 是否满足查找条件
      left = mid + 1; // 结果落在 [mid+1, right] 区间
    else
      right = mid; // 结果落在 [left, mid] 区间
  }
  return left;
}
```

上述例题【[704. 二分查找](https://leetcode-cn.com/problems/binary-search/) 】套用 **模板一** 的写法：

```C++
int binarySearch(vector<int> &nums, int target) {
  int left = 0, right = nums.size() - 1;
  while (left < right) {
    // 防止溢出，结果等同于 (left + right)/2
    int mid = left + ((right - left) / 2);
    // 检查 mid，比较 nums[mid] 和 target
    if (nums[mid] < target)
      // 结果落在 [mid+1, right] 区间
      left = mid + 1;
    else
      // 结果落在 [left, mid] 区间
      right = mid;
  }
  return nums[left] == target ? left : -1;
}
```

## 模板二

当区间 `[left, right]` 的更新操作是 `left = mid; right = mid - 1;` 时，计算 `mid` 需要加 `1`。

通用模板写法：

```C++
int binarySearch(int left, int right) {
  while (left < right) {
    // 注意防止溢出
    int mid = (left + right + 1) / 2;
    if (check(mid))
      // 结果落在 [left, mid-1]
      right = mid - 1;
    else
      // 结果落在 [mid, right]
      left = mid;
  }
  return left;
}
```

上述例题【[704. 二分查找](https://leetcode-cn.com/problems/binary-search/) 】套用 **模板二** 的写法：

```C++
int binarySearch(vector<int> &nums, int target) {
  int left = 0, right = nums.size() - 1;
  while (left < right) {
    // 防止溢出，结果等同于 (left + right + 1)/2
    int mid = left + ((right - left + 1) / 2);
    // 检查 mid，比较 nums[mid] 和 target
    if (nums[mid] > target)
      // target 落在 [left, mid-1]
      right = mid - 1;
    else
      // target 落在 [mid, right]
      left = mid;
  }
  return nums[left] == target ? left : -1;
}
```

## 求同存异

使用这两个模板的**优势**：

1. 不用考虑循环结束时返回 `left` 还是 `right`，因为最后退出时 `left == right`
2. 不用考虑循环条件是用 `< ` 还是 `<=`，直接都用 `<`
3. 非常适用于求一些分界点的问题

### 共同点

这两个模板写法的 **共同点**：

1. 查找区间都是 **左闭右闭** 区间：`[left, right]`
2. 循环判断条件都是用 `left < right`
3. 循环退出条件都是 `left == right`。查找过程一定会在 $O(logn)$ 的时间内终止，即使中间遇到 `nums[mid] == target` 也不会结束，而是继续收缩区间，直到区间长度为 `1`。
4. 在循环终止（查找结束）之后，判断一下 `left` 或者 `right` 是否符合要求即可，如果不符合要求，说明答案不存在

### 不同点

共同点比较好理解，但更值得注意的是，**这两个模板写法的不同点**：

1. 更新 `mid` 的计算方式不同
2. `check(mid)` 不同，所以更新 `left` 和 `right` 也会有一些差别

### Q & A

#### 1. 为什么模板二计算 mid 会加 1

原因：**避免进入死循环**。

首先，可以明确的是，两个模板在查找过程中都是区间长度不断减半，直到区间长度为 `1`，即 `left == right` 时退出循环，然后在循环外面检查最后的查找结果 `nums[left]` 是否为 `target`。

如果 **模板二** 更新 `mid` 采用 `mid = (left + right)/2`，在**最后一次查找时会陷入死循环**。例如，对于 `nums = [3,4]`，`target ` 为 `4`：

- 第一次查找，`left = 0，right = 1，mid = 0`，比较得到 `nums[mid] < target`，更新 `left = mid`
- 第二次查找，还是进入相同的条件分支，**陷入死循环**。

而更新 `mid` 采用 `mid = (left + right + 1) / 2` 时：

- 第一次检查 `left = 0, right = 1, mid = 1`，更新 `left = mid`，**退出循环**

也可以通过边界条件来理解，这两个模板最后都是要收缩到区间 `[left, left+1]` 里进行最后一次 `check`：

- 对于模板一，计算得到 `mid = left`，区间往左收缩是进入 `right = mid` ，区间往右收缩是进入 `left = mid + 1`，结束循环，所以更新 `mid` 要用 `mid = (left + right)/2`，
- 对于模板二，计算得到 `mid = right`，区间往左收缩是进入`right = mid - 1` ，区间往右收缩是进入 `left = mid`，结束循环，所以更新 `mid` 要用 `mid = (left + right + 1)/2` 

#### 2. 如何选择使用模板

一般写二分的思考顺序是这样的：通过题目背景 **确定 `check(mid)` 的逻辑，判断答案落在左半区间还是右半区间**：

- `target` 属于右半区间，则右半区间是 `[mid+1, right]`，左半区间是 `[left, mid]`，区间更新方式是   ` left = mid + 1; right = mid;`，此时用第一个模板；
- `target` 属于左半区间，则左半区间是 `[left, mid-1]`，右半区间是 `[mid, right]`，区间更新方式是 `right = mid - 1; left = mid;`，此时用第二个模板；

这种区间划分方式将  `check(mid) == targrt` 分支的逻辑合并到 `check(mid) > targrt` 或 `check(mid) < targrt` 分支，不断将区间长度减半，具有更好的适用性，尤其适用于求一些分界点的问题。

例题：

- [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)  
- [69. x 的平方根 ](https://leetcode-cn.com/problems/sqrtx/)

解释：

1. 查找元素 `target` 的第一个位置，相当于查找 `大于等于 target` 的第一个元素位置：
   
   - 如果 `nums[mid] < target`，此时 `target` 应该落在 **右半区间** `[mid+1, right]`；
   - 如果 `nums[mid] >= target`，此时 `target` 应该落在 **左半区间** `[left, mid]`，因为 `mid` 可能就是 `target`，所以还需要进一步比较；
   
   因而选择 `check(mid)`为 `nums[mid] < target`，区间更新条件分别是 `left = mid + 1; right = mid;`

```c++
int searchFirst(vector<int> &nums, int target) {
    int left = 0, right = nums.size() - 1;
    while (left < right) {
      int mid = (left + right) >> 1;
      if (nums[mid] < target)
        left = mid + 1;
      else
        right = mid;
    }
    return nums[left] == target ? left : -1;
  }
```

2. 查找元素 `target` 的最后一个位置，相当于查找 `小于等于 target` 的最后一个元素位置：
- 如果 `nums[mid] > target`，此时 `target` 应该落在 **左半区间** `[left, mid-1]`；
- 如果 `nums[mid] <= target`，此时 `target` 应该落在 **右半区间** `[mid, right]`，因为 `mid` 可能就是 `target`，所以还需要进一步比较

因而选择 `check(mid)` 为 `nums[mid] > target`，区间更新条件分别是 `right = mid-1; left = mid;`

```c++
int searchLast(vector<int> &nums, int target) {
  int left = 0, right = nums.size() - 1;
  while (left < right) {
    int mid = (left + right + 1) / 2;
    if (nums[mid] <= target)
      left = mid;
    else
      right = mid - 1;
  }
  return nums[left] == target ? left : -1;
}
```

3. 计算 `x` 的算术平方根，结果只保留整数部分：由于 `x` 平方根的整数部分是满足 `k*k <= x ` 的 `最大 k 值`
- 如果 `mid * mid > x  `，那么结果应该落在 **左半区间** `[left, mid-1]`
- 如果 `mid * mid <= x  `，那么结果应该落在 **右半区间** `[mid, right]`

因而选择 `check(mid)` 为 `mid*mid > x`，区间更新条件分别是 `right = mid-1; left = mid;`

```c++
int mySqrt(int x) {
  int left = 0, right = x;
  while (left < right) {
    // 防止值溢出
    int mid = (right + left + 1LL) / 2;
    if (mid > x / mid)
      // 结果在左半区间 [left, mid-1]
      right = mid - 1;
    else
      // 结果在右半区间 [mid, right]
      left = mid;
  }
  return left;
}
```

## 解决问题

| 题目                                                                                                                     | 题解                                                                                                                                       | 难度  |
|:---------------------------------------------------------------------------------------------------------------------- |:----------------------------------------------------------------------------------------------------------------------------------------:|:---:|
| [4.寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)                                        | [LeetCode 题解](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-2/) | 困难  |
| [33. 搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)                                       | [LeetCode 题解](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/solution/by-ellisonzhao-iwux/)                             | 中等  |
| [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/) | [LeetCode 题解](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/solution/by-ellisonzhao-h2ee/)    | 中等  |
| [35. 搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)                                                 | [LeetCode 题解](https://leetcode-cn.com/problems/search-insert-position/solution/35-sou-suo-cha-ru-wei-zhi-by-ellisonzhao-qa8m/)           | 简单  |
| [69. x 的平方根 ](https://leetcode-cn.com/problems/sqrtx/)                                                                 | [LeetCode 题解](https://leetcode-cn.com/problems/sqrtx/solution/by-ellisonzhao-9pk9/)                                                      | 简单  |
| [74. 搜索二维矩阵](https://leetcode-cn.com/problems/search-a-2d-matrix/)                                                     | [LeetCode 题解](https://leetcode-cn.com/problems/search-a-2d-matrix/solution/by-ellisonzhao-t1sj/)                                         | 中等  |
| [81. 搜索旋转排序数组 II](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)                                 | [LeetCode 题解](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/solution/by-ellisonzhao-0fmi/)                          | 中等  |
| [153. 寻找旋转排序数组中的最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)                           | [LeetCode 题解](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/solution/by-ellisonzhao-l0ze/)                       | 中等  |
| [154. 寻找旋转排序数组中的最小值 II](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)                     | [LeetCode 题解](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/solution/by-ellisonzhao-tkes/)                    | 困难  |
| [162. 寻找峰值](https://leetcode-cn.com/problems/find-peak-element/)                                                       | [LeetCode 题解](https://leetcode-cn.com/problems/find-peak-element/solution/gong-shui-san-xie-noxiang-xin-ke-xue-xi-qva7v/)                | 中等  |
| [240. 搜索二维矩阵 II](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/)                                              | [LeetCode 题解](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/solution/by-ellisonzhao-20us/)                                      | 中等  |
| [367. 有效的完全平方数](https://leetcode-cn.com/problems/valid-perfect-square/)                                                | [LeetCode 题解](https://leetcode-cn.com/problems/valid-perfect-square/solution/by-ellisonzhao-nhfs/)                                       | 简单  |

