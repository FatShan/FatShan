# 2023.3.16

[2488. 统计中位数为 K 的子数组](https://leetcode.cn/problems/count-subarrays-with-median-k/)

## 题目

给你一个长度为 `n` 的数组 `nums` ，该数组由从 `1` 到 `n` 的 **不同** 整数组成。另给你一个正整数 `k` 。

统计并返回 `nums` 中的 **中位数** 等于 `k` 的非空子数组的数目。

注意：

数组的中位数是按 **递增** 顺序排列后位于 **中间** 的那个元素，如果数组长度为偶数，则中位数是位于中间靠 **左** 的那个元素。
  
例如，`[2,3,1,4]` 的中位数是 `2` ，`[8,4,3,5,1]` 的中位数是 `4` 。
  
子数组是数组中的一个连续部分。

## 方法

**数组转换**、**前缀和**

将大于k的数转换成1，小于k的数转换成-1。

从左往右遍历一遍，k左侧的数记录和，k和k右侧的数记录符合条件的子数组。

只要两个子数组的和相同或差一，那么两数组之差的和就是0或-1。

## 代码

``` cpp
class Solution {
public:
    inline int sign(int num) {
        if (num == 0) {
            return 0;
        }
        return num > 0 ? 1 : -1;
    }

    int countSubarrays(vector<int>& nums, int k) {
        int n = nums.size();
        int kIndex = -1;
        for (int i = 0; i < n; i++) {
            if (nums[i] == k) {
                kIndex = i;
                break;
            }
        }
        int ans = 0;
        unordered_map<int, int> counts;
        counts[0] = 1;
        int sum = 0;
        for (int i = 0; i < n; i++) {
            sum += sign(nums[i] - k);
            if (i < kIndex) {
                counts[sum]++;
            } else {
                int prev0 = counts[sum];
                int prev1 = counts[sum - 1];
                ans += prev0 + prev1;
            }
        }
        return ans;
    }
};
```

## 优化思考
