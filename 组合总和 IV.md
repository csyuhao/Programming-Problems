# 组合总和 IV

## 题目描述

给你一个由**不同**整数组成的数组```nums```，和一个目标整数```target```。请你从```nums```中找出并返回总和为```target```的元素组合的个数。

### 示例

```
示例1：nums = [1, 2, 3], target = 4
输出：7

示例2：nums = [9], target = 3
输出：0
```

**备注**
- ```1 <= nums.length <= 200```
- ```1 <= nums.length <= 1000```
- ```nums```中所有的元素**互不相同**
- ```1 <= target <= 1000```

## 解释说明

我们首先定义$f[i]$为凑成总和为$i$的方案数，那么由于```nums```都是正整数，因此显然有初始化条件$f[0] = 1$，同时最终答案为$f[\text{target}]$。由于每个元素都可以被选择无数次，因此在计算总和时，我们保证```nums```中的每个元素都被考虑到即可.

```C++
class Solution {
public:
    int combinationSum4(vector<int>& nums, int target) {
        vector<int> f(target + 1,0); // or vector<unsigned long long> f(target + 1,0); 就不用做取模的操作了
        f[0] = 1;
        for(int i = 1; i <= target; i++){
            for(auto x : nums){
                // C++计算的中间结果会溢出，但因为最终结果是int
                // 因此每次计算完都对INT_MAX取模，0LL是将计算结果提升到long long类型
                if(i >= x){
                    f[i] =(0LL + f[i] + f[i - x]) % INT_MAX;
                }
            }
        }
        return f[target];
    }
};
```