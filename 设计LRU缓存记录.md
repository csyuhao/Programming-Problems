# 设计LRU缓存记录

## 题目描述

设计LRU缓存结构，该结构在构造时确定大小，假设大小为K，并有如下两个功能
- set(key, value)：将记录(key, value)插入该结构
- get(key)：返回key对应的value值

**[要求]**

1. set和get方法的时间复杂度为O(1)
2. 某个key的set或get操作一旦发生，认为这个key的记录成了最常使用的。
3. 当缓存的大小超过K时，移除最不经常使用的记录，即set或get最久远的。

**输入说明**

* 若opt=1，接下来两个整数x, y，表示set(x, y)
* 若opt=2，接下来一个整数x，表示get(x)，若x未出现过或已被移除，则返回-1
对于每个操作2，输出一个答案

### 示例1

**输入**
```
[[1, 1, 1], [1, 2, 2], [1, 3, 2], [2, 1], [1, 4, 4], [2, 2]], 3
```
**返回值**
```
[1, -1]
```
**说明**
```
第一次操作后：最常使用的记录为("1", 1)
第二次操作后：最常使用的记录为("2", 2)，("1", 1)变为最不常用的
第三次操作后：最常使用的记录为("3", 2)，("1", 1)还是最不常用的
第四次操作后：最常用的记录为("1", 1)，("2", 2)变为最不常用的
第五次操作后：大小超过了3，所以移除此时最不常使用的记录("2", 2)，加入记录("4", 4)，并且为最常使用的记录，然后("3", 2)变为最不常使用的记录
```

**备注**

$1 \leq K \leq 10^5 \\ -2 \times 10^9 \leq x, y \leq 2 \times 10^9$

## 解释

**LRU的每次操作(get,put)都会将节点放入链表首部。需要自定义双向链表。**

在下边的实现中，我们可以定义一个```HashMap```（C++中是```map<DoubleLinkNode*, int>```实现DoubleLinkNode* --> key的快速查找（时间复杂度降低到$O(n)$。但是我为了熟悉双向链表，就直接采用从头遍历的方法。

```C++
#include <iostream>
#include <vector>
using namespace std;


struct DoubleLinkNode {
    DoubleLinkNode* prev = nullptr, * next = nullptr;
    int _val = 0, _key = 0;
    DoubleLinkNode(int k, int v) {
        prev = next = nullptr;
        _key = k;
        _val = v;
    }

    DoubleLinkNode(DoubleLinkNode& node) {
        _key = node._key;
        _val = node._val;
        prev = node.prev;
        next = node.next;
    }
};

class LRUCache {
private:
    DoubleLinkNode* head = nullptr;
    int maxLen = 0, curLen = 0;

    DoubleLinkNode* find(int k) {
        DoubleLinkNode* cur = head;
        while (cur) {
            if (cur->_key == k) {
                return cur;
            }
            cur = cur->next;
        }
        return nullptr;
    }

    void moveToHead(DoubleLinkNode* cur) {
        if (cur == head) {
            return;
        }
        cur->prev->next = cur->next;
        if (cur->next) {
            cur->next->prev = cur->prev;
        }
        cur->next = head;
        head->prev = cur;
        head = cur;
    }

    void insertHead(DoubleLinkNode* cur) {
        if (head != nullptr) {
            cur->next = head;
            head->prev = cur;
            head = cur;
        }
        else {
            head = cur;
        }
        curLen += 1;
        if (curLen > maxLen) {
            cur = head;
            while (cur && cur->next) {
                cur = cur->next;
            }
            if (cur) {
                cur->prev->next = nullptr;
                delete cur;
                curLen -= 1;
            }
        }
    }
public:
    LRUCache(int k) {
        this->maxLen = k;
        this->curLen = 0;
        head = nullptr;
    }

    void set(int k, int v) {
        DoubleLinkNode* cur = find(k);
        if (cur) {
            moveToHead(cur);
            return;
        }
        cur = new DoubleLinkNode(k, v);
        insertHead(cur);
    }

    int get(int k) {
        DoubleLinkNode* cur = find(k);
        if (cur == nullptr) {
            return -1;
        }
        moveToHead(cur);
        return cur->_val;
    }
    ~LRUCache() {
        DoubleLinkNode* cur = head, * tmp = nullptr;
        while (cur) {
            tmp = cur;
            cur = cur->next;
            delete tmp;
        }
    }
};



class Solution {
public:
    /**
     * lru design
     * @param operators int整型vector<vector<>> the ops
     * @param k int整型 the k
     * @return int整型vector
     */
    vector<int> LRU(vector<vector<int> >& operators, int k) {
        // write code here
        int size = operators.size();
        LRUCache cache(k);
        vector<int> ans;
        for (int idx = 0; idx < size; idx++) {
            if (operators[idx][0] == 1) {
                cache.set(operators[idx][1], operators[idx][2]);
            }
            else {
                ans.push_back(cache.get(operators[idx][1]));
            }
        }
        return ans;
    }
};

int main(int argc, char** argv) {
    vector<vector<int>> operators = {
        {1, 1, 1},
        {1, 2, 2},
        {1, 3, 2},
        {2, 1},
        {1, 4, 4},
        {2, 2}
    };

    int k = 3;
    Solution solu;
    vector<int> ans = solu.LRU(operators, k);

    for (int idx = 0; idx < ans.size(); idx++) {
        cout << ans[idx] << " ";
    }
    cout << endl;
    return 0;
}
```