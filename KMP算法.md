# KMP算法

## 题目描述

给你一个非空模板串$S$，一个文本串$T$，问$S$在$T$中出现了多少次

### 示例1
```
输入："ababab", "abababab"
返回值：2
```

### 示例2
```
输入："abab", "abacabab"
返回值：1
```

## 解释说明

构建```next```数组，然后执行kmp算法
```C++
class Solution {
public:
    /**
     * 代码中的类名、方法名、参数名已经指定，请勿修改，直接返回方法规定的值即可
     *
     * 计算模板串S在文本串T中出现了多少次
     * @param S string字符串 模板串
     * @param T string字符串 文本串
     * @return int整型
     */
    int kmp(string S, string T) {
        // write code here
        int sSize = S.size(), tSize = T.size();
        vector<int> next(sSize + 1, 0);
        buildNextArray(next, S);

        int j = 0, ans = 0;
        for (int idx = 0; idx < tSize; idx++) {

            if (T[idx] == S[j]) {
                j++;
                if (j == sSize) {
                    ans += 1;
                    j = next[sSize] + 1;
                    idx = idx - sSize + 1;
                }
                continue;
            }
            if (T[idx] != S[j]) {
                j = next[j];
            }
            j++;
        }
        return ans;
    }

    void buildNextArray(vector<int>& next, string S) {
        int size = S.size();
        next[0] = -1;
        next[size] = -1;
        int k = -1;
        for (int idx = 1; idx < S.size(); idx++) {
            while (k != -1 && S[k + 1] != S[idx]) {
                k = next[k];
            }

            if (S[k + 1] == S[idx]) {
                k++;
            }
            next[idx] = k;
        }
    }
};
```