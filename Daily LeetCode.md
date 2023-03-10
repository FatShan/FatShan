# 2023.3.10

[1590. 使数组和能被 P 整除](https://leetcode.cn/problems/make-sum-divisible-by-p/)

## 题目
给你一个正整数数组 `nums`，请你移除 **最短** 子数组（可以为 空），使得剩余元素的 **和** 能被 p 整除。 **不允许** # 将整个数组都移除。

请你返回你需要移除的最短子数组的长度，如果无法满足题目要求，返回 `-1` 。

子数组 定义为原数组中连续的一组元素。

## 方法

关键词：前缀和、同余定理

1. 对数组元素取余求和
2. 若为`0`则返回`0`
3. 求出同余的所有子数组长度
4. 返回最小长度

> arr[0...n] sum mod p = x, **to find** subarr[j...k] sum mod p = x => **to find** subarr[0...j-1] sum mod p = subarr[0...k] (sum - x) mod p
> proof: this eq to (y - z) mod p = x <=> z mod p = (y - x) mod p
> ( y - z) mod p = x => y - z = kp + x
> => z = y - kp - x = (y - x) - kp = (y - x) mod p + k'p - kp
> => z mod p = (y - x) mod p

## 代码

```cpp
class Solution {
public:
    int minSubarray(vector<int>& nums, int p) {
        int x = 0;
        for (auto num : nums) {
            x = (x + num) % p; // 防止溢出
        }
        if (x == 0) {
            return 0;
        }
        unordered_map<int, int> index;
        int y = 0, res = nums.size(); // y:子数组[0...i-1]的余数
        for (int i = 0; i < nums.size(); i++) {
            index[y] = i; // 记录余数，重复时，只需要记录较长子数组的余数
            y = (y + nums[i]) % p;
            if (index.count((y - x + p) % p) > 0) { // 子数组[0...i-1]-x的余数 = 子数组[0...j-1]的余数
                res = min(res, i - index[(y - x + p) % p] + 1);
            }
        }
        return res == nums.size() ? -1 : res;
    }
};
```

## 优化思考

## 相关阅读

[前缀和思想的理解与运用](https://leetcode.cn/circle/discuss/sv2auZ/)
