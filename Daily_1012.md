# 2023.3.20

[1012. 至少有 1 位重复的数字](https://leetcode.cn/problems/numbers-with-repeated-digits/)

## 题目

给定正整数 `n`，返回在 `[1, n]` 范围内具有 **至少** 1 位 重复数字的正整数的个数。

## 方法

数位DP、记忆化搜索

正向解题比较困难，可以考虑补集：`[1,n]`范围内无重复数字的正整数个数。

从最高位开始填入数码，用掩码记录已经填入的数码（前缀0不计入掩码）。假设当前填入第i位，如果前面`i-1`位与n中对应位置的数码相同，则第`i`位可填数码小等于`n`的第`i`位，否则可填入全部数码。

记当前可填入的最大数码为`t`，检查数码`k∈[0, t]`是否出现在掩码中，找到符合条件的数码并填入第`i`位。下面考虑第`i+1`位。

如果掩码=0且`k`=0，说明前面全是前缀0，掩码不变；否则掩码第k位设为1。重复上述过程。

> 注意：如果前面的数码与n中对应的数码不同，则符合条件的正整数个数只与掩码有关。



## 代码

``` cpp
class Solution {
public:
    vector<vector<int>> dp;

    int f(int mask, const string &sn, int i, bool same) {
        if (i == sn.size()) {
            return 1;
        }
        if (!same && dp[i][mask] >= 0) {
            return dp[i][mask];
        }
        int res = 0, t = same ? (sn[i] - '0') : 9;
        for (int k = 0; k <= t; k++) {
            if (mask & (1 << k)) {
                continue;
            }
            res += f(mask == 0 && k == 0 ? mask : mask | (1 << k), sn, i + 1, same && k == t);
        }
        if (!same) {
            dp[i][mask] = res;
        }
        return res;
    }

    int numDupDigitsAtMostN(int n) {
        string sn = to_string(n);
        dp.resize(sn.size(), vector<int>(1 << 10, -1));
        return n + 1 - f(0, sn, 0, true);
    }
};
```

## 优化思考

组合数学



## 相关阅读

[数位 DP](https://oi-wiki.org/dp/number/)
