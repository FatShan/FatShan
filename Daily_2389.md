# 2023.3.17

[2389. 和有限的最长子序列](https://leetcode.cn/problems/longest-subsequence-with-limited-sum/)

## 题目

给你一个长度为 `n` 的整数数组 `nums` ，和一个长度为 `m` 的整数数组 `queries` 。

返回一个长度为 `m` 的数组 `answer` ，其中 `answer[i]` 是 `nums` 中元素之和小于等于 `queries[i]` 的 **子序列** 的 **最大** 长度  。

**子序列** 是由一个数组删除某些元素（也可以不删除）但不改变剩余元素顺序得到的一个数组。

## 方法

排序。对每个和数，贪心地找到最长子数组（已排序）。

## 代码

``` cpp
class Solution {
public:
    vector<int> answerQueries(vector<int>& nums, vector<int>& queries) {
        sort(nums.begin(),nums.end());
        auto l=nums.begin();
        auto r=nums.begin();
        for(int i=0;i<queries.size();++i)
        {
            while(r!=nums.end() && accumulate(l,r,0)<queries[i])
                r++;
            while(r!=nums.begin() && accumulate(l,r,0)>queries[i])
                r--;
            queries[i]=r-l;
        }
        return queries;
    }
};
```

## 优化思考

每次寻找都要求和，可以把求和的操作先做了。然后使用二分查找。

``` cpp
class Solution {
public:
    vector<int> answerQueries(vector<int>& nums, vector<int>& queries) {
        sort(nums.begin(), nums.end());
        for (int i = 1; i < nums.size(); i++) {
            nums[i] += nums[i - 1];
        }
        vector<int> ans;
        for (auto& q : queries) {
            q=upper_bound(nums.begin(), nums.end(), q) - nums.begin();
        }
        return queries;
    }
};
```
