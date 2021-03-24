# 股票的最大收益II

## 题目描述

假定你知道某只股票每一天价格的变动。
你最多可以同时持有一只股票。但你最多只能进行两次交易（一次买进和一次卖出记为一次交易。买进和卖出均无手续费）。
请设计一个函数，计算你所能获得的最大收益。

**备注**
```
总天数不大于200000，保证股票每一天的价格在[1, 100]范围内。
```

### 示例1

**输入**
```
[8, 9, 3, 5, 1, 3]
```

**返回值**
```
4
```

**说明**
```
第三天买进，第四天卖出，第五天买进，第六天卖出。总收益为4。
```

## 解释说明

我们使用$5$个变量保存：不操作的收益、第一次买入的收益、第一次卖出的收益、第二次买入的收益、第二次卖出的收益。
假设第$i$天的价格为$prices[i]$：
- 当第$i$天不操作时，$dp[i][0] = 0$
- 当第$i$天第一次买入时，$dp[i][1] = dp[i - 1][0] - prices[i]$
- 当第$i$天第一次卖出时，$dp[i][2] = dp[i - 1][1] + prices[i]$
- 当第$i$天第二次买入时，$dp[i][3] = dp[i - 1][2] - prices[i]$
- 当第$i$天第二次卖出时，$dp[i][4] = dp[i - 1][3] + prices[i]$

```C++
class Solution {
public:
    /**
     * 代码中的类名、方法名、参数名已经指定，请勿修改，直接返回方法规定的值即可
     * 两次交易所能获得的最大收益
     * @param prices int整型vector 股票每一天的价格
     * @return int整型
     */
    int maxProfit(vector<int>& prices) {
        // write code here
        int size = prices.size();
        if (size == 0){
            return 0;
        }

        vector<vector<int>> dp(size, vector<int>(5, 0));
        dp[0][1] = -prices[0];
        dp[0][3] = -prices[0];
        for (int idx = 1; idx < size; idx++){
            dp[idx][0] = dp[idx - 1][0];
            dp[idx][1] = max(dp[idx - 1][1], dp[idx - 1][0] - prices[idx]);
            dp[idx][2] = max(dp[idx - 1][2], dp[idx - 1][1] + prices[idx]);
            dp[idx][3] = max(dp[idx - 1][3], dp[idx - 1][2] - prices[idx]);
            dp[idx][4] = max(dp[idx - 1][4], dp[idx - 1][3] + prices[idx]);
        }
        return dp[size - 1][4];
    }
};
```