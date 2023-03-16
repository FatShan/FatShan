# 2023.3.15

[1615. 最大网络秩](https://leetcode.cn/problems/maximal-network-rank/)

## 题目

`n` 座城市和一些连接这些城市的道路 `roads` 共同组成一个基础设施网络。每个 `roads[i] = [ai, bi]` 都表示在城市 `ai` 和 `bi` 之间有一条双向道路。

两座不同城市构成的 **城市对** 的 **网络秩** 定义为：与这两座城市 **直接** 相连的道路总数。如果存在一条道路直接连接这两座城市，则这条道路只计算 **一次** 。

整个基础设施网络的 **最大网络秩** 是所有不同城市对中的 **最大网络秩** 。

给你整数 `n` 和数组 `roads`，返回整个基础设施网络的 **最大网络秩** 。

## 方法

1. 计算每个点的度，记录邻接矩阵。
2. 找到最大值Fmax和次大值Smax，并记录所有Fmax和Smax的下标，其中Fmax!=Smax。
3. 分类讨论：Fmax数量大于1，则秩为`2*Fmax-1` / `2*Fmax`；若Fmax数量为1，Smax数量大于1，则秩为`Fmax+Smax-1` / `Fmax+Smax`；若Fmax数量为1，Smax数量为1，则秩为`Fmax+Smax-1` / `Fmax+Smax`。

## 代码

``` cpp
class Solution {
public:
    int maximalNetworkRank(int n, vector<vector<int>>& roads) {
        int count[n];
        bool link[n][n];
        memset(count,0,4*n);
        for(int i=0;i<n;++i) memset(link[i],false,n);
        for(auto& l : roads)
        {
            link[l[0]][l[1]]=true;
            link[l[1]][l[0]]=true;
            count[l[0]]++;
            count[l[1]]++;
        }
        int Fmax=0,Smax=0;
        vector<int> Fset,Sset;
        for(int i=0;i<n;++i)
        {
            if(count[i]>Fmax)
            {
                Smax=Fmax;
                Fmax=count[i];
                Sset=Fset;
                Fset.clear();
                Fset.push_back(i);
            }
            else if(count[i]==Fmax)
            {
                Fset.push_back(i);
            }
            else if(count[i]>Smax)
            {
                Smax=count[i];
                Sset.clear();
                Sset.push_back(i);
            }
            else if(count[i]==Smax)
            {
                Sset.push_back(i);
            }
        }
        int ans=0,o,t;
        if(Fset.size()>=2)
        {
            ans=2*count[Fset[0]];
            for(int i=0;i<Fset.size();++i)
            {
                for(int j=i+1;j<Fset.size();++j)
                {
                    o=Fset[i];
                    t=Fset[j];
                    if(!link[o][t]) return ans;
                }
            }
            ans--;
        }
        else if(Sset.size()>=2)
        {
            o=Fset[0];
            for(int i=0;i<Sset.size();++i)
            {
                t=Sset[i];
                ans=count[o]+count[t];
                if(!link[o][t]) return ans;
            }
            ans--;
        }
        else
        {
            o=Fset[0];
            t=Sset[0];
            ans=count[o]+count[t]-(link[o][t]?1:0);
        }
        
        return ans;
    }
};
```

## 优化思考
