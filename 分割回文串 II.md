# 分割回文串

## 题目描述

给你一个字符串```s```，请你将```s```分割成一些子串，使每个子串都是回文。

返回符合要求的 最少分割次数 。

### 示例1

**输入**
```
s = "abb"
```
**输出**
```
1
```
**解释**
```
只需一次分割就可将 s 分割成 ["aa","b"] 这样两个回文子串。
```

### 示例2

**输入**
```
s = "a"
```
**输出**
```
0
```

### 示例3

**输入**
```
s = "ab"
```
**输出**
```
1
```

## 解释说明

设$f[i]$表示字符串的前缀$s[0 \cdots i]$的最少分割次数。要想得出$f[i]$的值，我们可以考虑枚举$s[0..i]$分割出的最后一个回文串，这样我们就可以写出状态转移方程：

$$f[i] = \min _{0 \leq j \leq i} \{ f[j] \} + 1 \text{其中} s[j + 1, \cdots, i] \text{是一个回文串}$$
​	
即我们枚举最后一个回文串的起始位置$j+1$，保证$s[j + 1 \cdots i]$是一个回文串，那么$f[i]$就可以从$f[j]$转移而来，附加$1$次额外的分割次数。

注意到上面的状态转移方程中，我们还少考虑了一种情况，即$s[0 \cdots i]$本身就是一个回文串。此时其不需要进行任何分割，即：

$f[i] = 0$

那么我们如何知道$s[j + 1 \cdots i]$或者$s[0 \cdots i]$是否为回文串呢？我们可以使用预处理方法，将字符串$s$的每个子串是否为回文串预先计算出来，即：

设 $g(i, j)$ 表示 $s[i . . j]$ 是否为回文串, 那么有状态转移方程:
$$
g(i, j)=\left\{\begin{array}{ll}
\text { True, } & i \geq j \\
g(i+1, j-1) \wedge(s[i]=s[j]), & \text { otherwise }
\end{array}\right.
$$
其中 $\wedge$ 表示逻辑与运算, 即 $s[i \cdots j]$ 为回文串, 当且仅当其为空串 $(i>j)$、其长度为 $1(i=j)$、或者首尾字符相同且 $s[i+1 \cdots j-1]$ 为回文串。

```C++
class Solution {
public:
    int minCut(string s) {
        int size = s.size();
        int i = 0, j = 0;
        vector<vector<bool>> dp(size, vector<bool>(size, true));

        for (i = size - 1; i >= 0; i--) {
            for (j = i + 1; j < size; j++) {
                dp[i][j] = dp[i + 1][j - 1] && (s[i] == s[j]);
            }
        }

        int n = 0;
        vector<int> ans(size, 0);
        for (i = 0; i < size; i++) {
            if (dp[0][i]) {
                ans[i] = 0;
                continue;
            }
            n = size + 10;
            for (j = 0; j < i; j++) {
                if (dp[j + 1][i] == true) {
                    n = min(n, ans[j] + 1);
                }
            }
            ans[i] = n;
        }

        return ans[size - 1];
    }
};
```

