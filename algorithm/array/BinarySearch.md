# 二分查找

## 1. 二分查找思想

二分查找也称为折半查找，是一种在排序数组中查找特定元素的高效的搜索算法，其本质还是**分治的思想**。

它的基本原理是将有序的列表分成两半，然后查看目标值是否在中间的值，如果目标值小于中间值，则在左半部分进行查找，否则在右半部分进行查找。这样**每次都可以排除一半的搜索区间**，使得它在处理大数据集时非常高效。

时间复杂度：O(logn)

空间复杂度：O(1)

## 2. 实现

我们定义 target 是在一个在左闭右闭的区间里，也就是[l, r] ，因为定义target在[l, r]区间，所以有如下两点：

+ while**循环条件要使用 <=** ，因为l == r是有意义的；
+ if (nums[m] > target) r 要赋值为 m - 1，因为当前这个nums[m]一定不是target，那么接下来要查找的左区间结束下标位置就是 m - 1，反之同理；

### 查找目标值版本

```cpp
int binary_search(vector<int>& nums, int target) {
    int l = 0, r = nums.size() - 1; // [l, r]
    while (l <= r) { // 当l == r，区间[l, r]依然有效，所以用 <=
        int m = l + ((r - l) >> 1);
        if (nums[m] > target) {
            r = m - 1; 
        } else if (nums[m] < target) {
            l = m + 1; 
        } else {
            return m; 
        }
    }
    // 未找到目标值
    return -1;
}
```

### 查找最接近目标值版本

```cpp
int binary_search(vector<int>& nums, int target) {
    int l = 0, r = nums.size() - 1; // [l, r]
    while (l <= r) {
        int m = l + ((r - l) >> 1);
        if (nums[m] > target) {
            r = m - 1;
        } else if (nums[m] < target) {
            l = m + 1;
        } else {
            return m;
        }
    }
    return l; // 大于等于target的最小值下标
    // return r; // 小于等于target的最大值下标
}
```
