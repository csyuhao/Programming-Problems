# LFU缓存结构设置

## 题目描述

一个缓存结构需要实现如下功能：
- ```set(key, value)```：将记录```(key, value)```插入该结构
- ```get(key)```：返回```key```对应的```value```值


但是缓存结构中最多放$K$条记录，如果新的第$K + 1$条记录要加入，就需要根据策略删掉一条记录，然后才能把新记录加入。

这个策略为：在缓存结构的$K$条记录中，哪一个```key```从进入缓存结构的时刻开始，被调用```set```或者```get```的次数最少，就删掉这个```key```的记录；
如果调用次数最少的```key```有多个，上次调用发生最早的```key```被删除。这就是LFU缓存替换算法。实现这个结构，$K$作为参数给出。

**要求**
```set```和```get```方法的时间复杂度为$O(1)$

- 若```opt = 1```，接下来两个整数```x, y```，表示```set(x, y)```
- 若```opt = 2```，接下来一个整数```x```，表示```get(x)```，若```x```未出现过或已被移除，则返回```-1```

对于每个操作```2```，输出一个答案。
```C++
struct Node {
    int key = 0, value = 0, freq = 0, timestamp = 0;
    Node() {}
    Node(int k, int v, int f, int t) {
        key = k;
        value = v;
        freq = f;
        timestamp = t;
    }
    Node(const Node& n) {
        key = n.key;
        value = n.value;
        freq = n.freq;
        timestamp = n.timestamp;
    }

    bool operator < (const Node& a) const{
        if (freq != a.freq) {
            return freq < a.freq;
        }
        return timestamp < a.timestamp;
    }
};

class Solution {
public:
    int sys_time = 0;
    int capacity = 0;
    set<Node> sortedSet;
    unordered_map<int, Node> cache;

    void init(int _capacity) {
        capacity = _capacity;
        sys_time = 0;
    }

    /**
     * lfu design
     * @param operators int整型vector<vector<>> ops
     * @param k int整型 the k
     * @return int整型vector
     */

    void set(int k, int v) {
        sys_time++;
        if (capacity == 0) {
            return;
        }
        auto it = cache.find(k);
        if (it == cache.end()) {
            if (cache.size() == capacity) {
                auto delNode = sortedSet.begin();
                cache.erase(delNode->key);
                sortedSet.erase(delNode);
            }
            Node newNode(k, v, 1, sys_time);
            cache[k] = newNode;
            sortedSet.insert(newNode);
            return;
        }
        Node node = it->second;
        sortedSet.erase(node);
        node.freq += 1;
        node.timestamp = sys_time;
        node.value = v;
        cache[k] = node;
        sortedSet.insert(node);
        return;
    }

    int get(int k) {
        sys_time++;
        if (capacity == 0) {
            return -1;
        }
        auto it = cache.find(k);
        if (it == cache.end()) {
            return -1;
        }
        Node node = it->second;
        sortedSet.erase(node);
        node.freq += 1;
        node.timestamp = sys_time;
        cache[k] = node;
        sortedSet.insert(node);
        return node.value;
    }
    vector<int> LFU(vector<vector<int> >& operators, int k) {
        // write code here
        vector<int> ans;

        init(k);

        for (auto& op : operators) {
            int operate = op[0];
            if (operate == 1) {
                set(op[1], op[2]);
            }
            else if (operate == 2) {
                ans.push_back(get(op[1]));
            }
            else {
                continue;
            }
        }
        return ans;
    }
};
```
