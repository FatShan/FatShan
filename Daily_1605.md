# 2023.3.14

[1605. 给定行和列的和求可行矩阵](https://leetcode.cn/problems/find-valid-matrix-given-row-and-column-sums/)

## 题目

给你两个非负整数数组 `rowSum` 和 `colSum` ，其中 `rowSum[i]` 是二维矩阵中第 `i` 行元素的和， `colSum[j]` 是第 `j` 列元素的和。换言之你不知道矩阵里的每个元素，但是你知道每一行和每一列的和。

请找到大小为 `rowSum.length` x `colSum.length` 的任意 **非负整数** 矩阵，且该矩阵满足 `rowSum` 和 `colSum` 的要求。

请你返回任意一个满足题目要求的二维矩阵，题目保证存在 **至少一个** 可行矩阵。

## 方法

贪心地从左上到右下遍历一遍，每次填尽可能大的值，更新行和列和即可。

## 代码

``` cpp
class Solution {
public:
    vector<vector<int>> restoreMatrix(vector<int>& rowSum, vector<int>& colSum) {
        vector<vector<int>> matrix(rowSum.size(),vector<int>(colSum.size(),0));
        for(int i=0;i<rowSum.size();++i)
        {
            if(rowSum[i]==0) continue;
            for(int j=0;j<colSum.size();++j)
            {
                if(rowSum[i]==0) break;
                if(colSum[j]==0) continue;
                matrix[i][j]=min(rowSum[i],colSum[j]);
                rowSum[i]-=matrix[i][j];
                colSum[j]-=matrix[i][j];
            }
        }
        return matrix;
    }
};
```

## 优化思考

## 相关阅读

