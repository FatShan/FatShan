# 2023.2.13

[1638. 统计只差一个字符的子串数目](https://leetcode.cn/problems/count-substrings-that-differ-by-one-character/)

## 题目

给你两个字符串 `s` 和 `t` ，请你找出 `s` 中的非空子串的数目，这些子串满足替换 **一个不同字符** 以后，是 `t` 串的子串。换言之，请你找到 `s` 和 `t` 串中 **恰好** 只有一个字符不同的子字符串对的数目。

比方说， `"computer" and "computation"` 只有一个字符不同： `'e'`/`'a'` ，所以这一对子字符串会给答案加 1 。

请你返回满足上述条件的不同子字符串对数目。

一个 **子字符串** 是一个字符串中连续的字符。

## 方法

要求唯一不同字符对的子串个数，可以：
- 首先求出所有不同的字符对
- 然后固定一个不同的字符对
- 接着求出以包含该字符对的符合条件的子串个数

使用dp，定义dpl[i+1][j+1]表示以s[i]t[j]结尾的相同子串个数，dpr[i][j]表示以s[i][j]开头的相同子串个数。

dpl[i+1][j+1] = dpl[i][j] + 1, when s[i] == t[j]
dpr[i][j] = dpr[i+1][j+1] + 1, when s[i] == t[j]

## 代码

``` cpp
class Solution {
public:
    int countSubstrings(string s, string t) {
        int m = s.size(), n = t.size();
        vector<vector<int>> dpl(m + 1, vector<int>(n + 1));// i，j结尾的相等字串个数
        vector<vector<int>> dpr(m + 1, vector<int>(n + 1));// i，j开始的相等字串个数
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                dpl[i + 1][j + 1] = s[i] == t[j] ? (dpl[i][j] + 1) : 0;
            }
        }
        for (int i = m - 1; i >= 0; i--) {
            for (int j = n - 1; j >= 0; j--) {
                dpr[i][j] = s[i] == t[j] ? (dpr[i + 1][j + 1] + 1) : 0;
            }
        }
        int ans = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (s[i] != t[j]) {// 以i，j为唯一不同字符对
                    ans += (dpl[i][j] + 1) * (dpr[i + 1][j + 1] + 1);
                    // i-1，j-1结尾，i+1，j+1开始
                }
            }
        }
        return ans;
    }
};
```

## 优化思考

### 一、通用化
dp思路可变成三维，dp[i+1][j+1][k]表示以s[i]t[j]结尾的、相差k个字符对的子串个数。在此题中，k=0,1。

那么，状态转移方程为：
s[i] == t[j] : dp[i+1][j+1][0] = dp[i][j][0] + 1
               dp[i+1][j+1][1] = dp[i][j][1]

s[i] != t[j] : dp[i+1][j+1][1] = dp[i][j][0] + 1

``` cpp
class Solution {
public:
    int countSubstrings(string s, string t) {
        unsigned m=s.size(), n=t.size(), k=1;
        vector<vector<vector<int>>> dp(m+1,vector<vector<int>>(n+1,vector<int>(k+1,0)));
        for(unsigned i=0;i<m;++i)
        {
            for(unsigned j=0;j<n;++j)
            {
                if(s[i]==t[j])
                {
                    dp[i+1][j+1][0]=dp[i][j][0]+1;
                    dp[i+1][j+1][1]=dp[i][j][1];
                }
                else
                {
                    dp[i+1][j+1][1]=dp[i][j][0]+1;
                }
            }
        }
        unsigned ans=0;
        for(unsigned i=1;i<=m;++i)
        {
            for(unsigned j=1;j<=n;++j)
            {
                ans+=dp[i][j][1];
            }
        }
        return ans;
    }
};
```

### 二、巧妙枚举

设定三个参数：i：s子串结尾下标，j：t子串结尾下标，k：s子串开始下标。

假定i==j，通过对简易例子的分析可得到性质：
1. 符合条件的k总在一个连续区间内；
2. 当s[i]!=t[i]时，k就会发生变化。

记$k_1$为上一个不同字符对的k，$k_0$为上上个不同字符对的k，则$k\in(k_0,k_1]$。因此，我们枚举i，维护$k_0$和$k_1$，累加$k_1-k_0$，由于$k_0$和$k_1$一开始不存在，初始化为-1。

再推广到i!=j的情况。枚举d=i-j，$k_0$和$k_1$初始化为i-1。

``` cpp
class Solution {
public:
    int countSubstrings(string s, string t) {
        int ans = 0, n = s.length(), m = t.length();
        for (int d = 1 - m; d < n; ++d) { // d=i-j, j=i-d
            int i = max(d, 0);
            for (int k0 = i - 1, k1 = k0; i < n && i - d < m; ++i) {
                if (s[i] != t[i - d])
                    k0 = k1, k1 = i;
                ans += k1 - k0;
            }
        }
        return ans;
    }
};
```

## 相关阅读
