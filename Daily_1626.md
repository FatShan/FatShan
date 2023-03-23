# 2023.3.23

[1626. 无矛盾的最佳球队](https://github.com/FatShan/FatShan/new/notebook)

## 题目

假设你是球队的经理。对于即将到来的锦标赛，你想组合一支总体得分最高的球队。球队的得分是球队中所有球员的分数 **总和** 。

然而，球队中的矛盾会限制球员的发挥，所以必须选出一支 **没有矛盾** 的球队。如果一名年龄较小球员的分数 **严格大于** 一名年龄较大的球员，则存在矛盾。同龄球员之间不会发生矛盾。

给你两个列表 `scores` 和 `ages`，其中每组 `scores[i]` 和 `ages[i]` 表示第 `i` 名球员的分数和年龄。请你返回 **所有可能的无矛盾球队中得分最高那支的分数** 。

## 方法

动态规划

先按分数排序，建立dp，dp[i]表示以第i位球员为球队的瓶颈时球队最大分数。dp中的最大值为答案。

## 代码

``` cpp
class Solution {
public:
    int bestTeamScore(vector<int>& scores, vector<int>& ages) {
        vector<pair<int,int>> info;
        for(unsigned i = 0; i < scores.size(); ++i)
        {
            info.push_back(pair<int,int>(scores[i],ages[i]));
        }
        sort(info.begin(), info.end());
        vector<unsigned> dp(scores.size(), 0);
        unsigned res = 0;
        for(unsigned i = 0; i < scores.size(); ++i)
        {
            for( unsigned j = 0; j < i; ++j)
            {
                if(info[j].second <= info[i].second)
                {
                    dp[i] = max(dp[i],dp[j]);
                }
            }
            dp[i] += info[i].first;
            res = max(res, dp[i]);
        }
        return res;
    }
};
```

## 优化思考

dp计算的时候，每次都要遍历[0,i-1]，

## 相关阅读
