# N皇后问题

## 题目描述

$N$皇后问题是指在$N \times N$的棋盘上要摆$N$个皇后，要求：任何两个皇后不同行，不同列也不再同一条斜线上，求给一个整数$n$，返回$N$皇后的摆法数。

### 示例1

**输入**
```
1
```

**返回值**
```
1
```

### 示例2

**输入**
```
8
```

**返回值**
```
92
```

**备注**

$1 \leq n \leq 14$


## 解释说明

使用$3$个无符号变量保存不能防止的位置。
```C++
class Solution {
public:
    /**
     *
     * @param n int整型 the n
     * @return int整型
     */
    int Nqueen(int n) {
        // write code here
        int ans = 0;
        unsigned col = 0, posDialog = 0, negDialog = 0;
        setQueen(ans, n, 0, col, posDialog, negDialog);
        return ans;
    }

    void setQueen(int& ans, int n, int step, unsigned col, unsigned posDialog, unsigned negDialog) {
        if (step == n) {
            ans += 1;
            return;
        }
        unsigned loc = 1 << (n - 1);
        if (step != 0) {
            posDialog = posDialog >> 1;
            negDialog = negDialog << 1;
        }
        while (loc != 0) {
            if (((loc & col) == 0) && ((loc & posDialog) == 0) && ((loc & negDialog) == 0)) {
                setQueen(ans, n, step + 1, col | loc, posDialog | loc, negDialog | loc);
            }
            loc = loc >> 1;
        }
        return;
    }
};
```