# 粉刷房子 III

## 题目描述

在一个小城市里，有```m```个房子排成一排，你需要给每个房子涂上```n```种颜色之一（颜色编号为```1```到```n```）。有的房子去年夏天已经涂过颜色了，所以这些房子不需要被重新涂色。我们将连续相同颜色尽可能多的房子称为一个街区。（比方说```houses = [1, 2, 2, 3, 3, 2, 1, 1]```，它包含```5```个街区```[{1}, {2,2}, {3,3}, {2}, {1,1}]```。）

给你一个数组```houses```，一个$m \times n$的矩阵```cost```和一个整数```target```，其中：
- ```houses[i]```：是第$i$个房子的颜色，$0$表示这个房子还没有被涂色；
- ```costs[i][j]```：是将第$i$个房子涂成颜色$j + 1$的花费。
请你返回房子涂色方案的最小总花费，使得每个房子都被涂色后，恰好组成```target```个街区。如果没有可用的涂色方案，请返回$-1$。

### 示例

```
示例1：houses = [0, 0, 0, 0, 0], cost = [[1, 10], [10, 1], [10, 1],[1, 10],[5, 1]], m = 5, n = 2, target = 3
输出：9
解释：房子涂色方案为[1, 2, 2, 1, 1] 此方案包含target = 3个街区，分别是[{1}, {2, 2}, {1, 1}]，涂色的总花费为9。

示例2：houses = [0, 2, 1, 2, 0], cost = [[1, 10],[10, 1],[10, 1],[1, 10],[5, 1]], m = 5, n = 2, target = 3
输出：11
解释：房子涂色方案为[2, 2, 1, 2, 2] 此方案包含target = 3个街区，分别是[{2， 2}, {1}, {2, 2}]，涂色的总花费为11。
```

## 解释说明

设$dp(i, j, k)$表示将$[0, i]$的房子都涂上颜色，最末尾的第$i$个房子的颜色为$j$，并且它属于第$k$个街区时，需要的最小花费。在进行状态转移时，我们需要考虑【第$i - 1$个房子的颜色】，这关系到【花费】以及【街区数量】的计算。

设第$i - 1$个房子的颜色为$j_0$，我们可以分情况讨论状态转移方程：
- 如果```houses[i] != -1```，说明第$i$个房子已经涂过颜色了。由于我们不能重复涂色，那么必须有```houses[i] = j```。我们可以写出在```houses[i] != j```时的状态转移方程：
$$
dp(i, j, k) = \infty \qquad \text{如果} houses[i] \neq -1 \text{并且} houses[i] \neq j
$$
> > 这里我们用极大值$\infty$表示不满足要求的状态，由于我们需要求出的是最少花费，因此极大值不会对状态转移产生影响。
- 如果```houses[i] = j```且$j = j_0$，那么第$i - 1$个房子和第$i$个房子属于一个街区，状态转移方程为：
$$
dp(i, j, k) = dp(i - 1, j, k), \qquad \text{如果} houses[i] = j
$$
- 如果```houses[i] = j```且$j \neq j_0$，那么它们属于不同的街区，状态转移方程为：
$$
dp(i, j, k) = \underset{j_0 \neq j}{\operatorname{min}} \; dp(i - 1, j, k), \qquad \text{如果} houses[i] = j
$$
- 如果```houses[i] = -1```，说明我们需要将第$i$个房子涂成颜色$j$，花费为```cost[i][j]```。若$j = j_0$，那么状态转移方程为：
$$
dp(i, j, k) = dp(i - 1, j, k) + costs[i][j], \qquad \text{如果} houses[i] = -1
$$
- 如果$j \neq j_0$，那么状态转移方程为：
$$
dp(i, j, k) = \underset{j_0 \neq j}{\operatorname{min}} \; dp(i - 1, j_0, k - 1) + costs[i][j], \qquad \text{如果} houses[i] = -1
$$
最终的答案为$\underset{j}{\operatorname{min}} \; dp(m - 1, j, target - 1)$。

```C++
class Solution {
private:
    // 极大值
    // 选择 INT_MAX / 2 的原因是防止整数相加溢出
    static constexpr int INFTY = INT_MAX / 2;

public:
    int minCost(vector<int>& houses, vector<vector<int>>& cost, int m, int n, int target) {
        // 将颜色调整为从 0 开始编号，没有被涂色标记为 -1
        for (int& c: houses) {
            --c;
        }

        // dp 所有元素初始化为极大值
        vector<vector<vector<int>>> dp(m, vector<vector<int>>(n, vector<int>(target, INFTY)));

        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (houses[i] != -1 && houses[i] != j) {
                    continue;
                }
                
                for (int k = 0; k < target; ++k) {
                    for (int j0 = 0; j0 < n; ++j0) {
                        if (j == j0) {
                            if (i == 0) {
                                if (k == 0) {
                                    dp[i][j][k] = 0;
                                }
                            }
                            else {
                                dp[i][j][k] = min(dp[i][j][k], dp[i - 1][j][k]);
                            }
                        }
                        else if (i > 0 && k > 0) {
                            dp[i][j][k] = min(dp[i][j][k], dp[i - 1][j0][k - 1]);
                        }
                    }

                    if (dp[i][j][k] != INFTY && houses[i] == -1) {
                        dp[i][j][k] += cost[i][j];
                    }
                }
            }
        }

        int ans = INFTY;
        for (int j = 0; j < n; ++j) {
            ans = min(ans, dp[m - 1][j][target - 1]);
        }
        return ans == INFTY ? -1 : ans;
    }
};
```