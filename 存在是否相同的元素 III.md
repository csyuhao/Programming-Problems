# 存在是否相同的元素 III

## 题目描述

给你一个整型数组```nums```和两个整数$k$和$t$。请你判断是否存在**两个不同的下标**$i$和$j$，使得```abs(nums[i] - nums[j]) <= t```，同时又满足```abs(i - j) <= k```。如果存在则返回```true```，不存在则返回```false```。


### 示例

```
示例1：nums = [1, 2, 3, 1], k = 3, t = 0
输出：true
```

```
示例2：nums = [1, 0, 1, 1], k = 1, t = 2
输出：true
```

```
示例3：nums = [1, 5, 9, 1, 5, 9], k = 2, t = 3
输出：false
```

**提示**
- $0 \leq nums.length \leq 2 \times 10^4$
- $- 2^{31} \leq nums[i] \leq 2^{31} - 1$
- $0 \leq k \leq 10^4$
- $0 \leq t \leq 2^{31} - 1$

## 解释说明

### 滑动窗口 + 有序集合

对于序列中每一个元素$x$左侧的至多$k$个元素，如果这$k$个元素中存在一个元素落在区间$[x - t, x + t]$中，我们就找到了一对符合条件的元素。注意到对于两个相邻的元素，它们各自的左侧的$k$个元素中有$k - 1$个是重合的。于是我们可以使用滑动窗口的思路，维护一个大小为$k$的滑动窗口，每次遍历到元素$x$时，滑动窗口中包含元素$x$前面的最多$k$个元素，我们检查窗口中是否存在元素落在区间$[x - t, x + t]$中即可。

如果使用队列维护滑动窗口内的元素，由于元素是无序的，我们只能对于每个元素都遍历一次队列来检查是否有元素符合条件。如果数组的长度为$n$，则使用队列的时间复杂度为$O(nk)$，会超出时间限制。

因此我们希望能够找到一个数据结构维护滑动窗口内的元素，该数据结构需要满足以下操作：
- 支持添加和删除指定元素的操作，否则我们无法维护滑动窗口；
- 内部元素有序，支持二分查找的操作，这样我们可以快速判断滑动窗口中是否存在元素满足条件，具体而言，对于元素$x$，当我们希望判断滑动窗口中是否存在某个数$y$落在区间$[x - t, x + t]$中，只需要判断滑动窗口中所有大于等于$x - t$的元素中的最小元素是否小于等于$x + t$即可。
- 
我们可以使用有序集合来支持这些操作。

```C++
class Solution {
public:
    bool containsNearbyAlmostDuplicate(vector<int>& nums, int k, int t) {
        int n = nums.size();
        set<int> rec;
        for (int i = 0; i < n; i++) {
            auto iter = rec.lower_bound(max(nums[i], INT_MIN + t) - t);
            if (iter != rec.end() && *iter <= min(nums[i], INT_MAX - t) + t) {
                return true;
            }
            rec.insert(nums[i]);
            if (i >= k) {
                rec.erase(nums[i - k]);
            }
        }
        return false;
    }
};
```

### 桶

对于元素$x$，其影响的区间为$[x - t, x + t]$。于是我们可以设定桶的大小为$t + 1$。如果两个元素同属一个桶，那么这两个元素必然符合条件。如果两个元素属于相邻桶，那么我们需要校验这两个元素是否差值不超过$t$。如果两个元素既不属于同一个桶，也不属于相邻桶，那么这两个元素必然不符合条件。

具体地，我们遍历该序列，假设当前遍历到元素$x$，那么我们首先检查$x$所属于的桶是否已经存在元素，如果存在，那么我们就找到了一对符合条件的元素，否则我们继续检查两个相邻的桶内是否存在符合条件的元素。

实现方面，我们将$\texttt{int}$范围内的每一个整数$x$表示为$x = (t + 1) \times a + b(0 \leq b \leq t)$的形式，这样$x$即归属于编号为$a$的桶。因为一个桶内至多只会有一个元素，所以我们使用哈希表实现即可。

```C++
class Solution {
public:
    int getID(int x, long w) {
        return x < 0 ? (x + 1ll) / w - 1 : x / w;
    }

    bool containsNearbyAlmostDuplicate(vector<int>& nums, int k, int t) {
        unordered_map<int, int> mp;
        int n = nums.size();
        for (int i = 0; i < n; i++) {
            long x = nums[i];
            int id = getID(x, t + 1ll);
            if (mp.count(id)) {
                return true;
            }
            if (mp.count(id - 1) && abs(x - mp[id - 1]) <= t) {
                return true;
            }
            if (mp.count(id + 1) && abs(x - mp[id + 1]) <= t) {
                return true;
            }
            mp[id] = x;
            if (i >= k) {
                mp.erase(getID(nums[i - k], t + 1ll));
            }
        }
        return false;
    }
};
```