# 合并$k$个已经排序的列表

## 题目描述

合并$k$个已排序的链表并将其作为一个已排序的链表返回。分析并描述其复杂度。 

### 示例1

**输入**
```
[{1, 2, 3}, {4, 5, 6, 7}]
```
**返回值**
```
{1, 2, 3, 4, 5, 6, 7}
```

## 解释

考虑归并排序，但是要注意当```lists.size() % 2 == 1```时，最后一个链表是无法合并的。


```C++
#include <iostream>
#include <vector>
#include <string>
using namespace std;

/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */

struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(NULL) {}
};

class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        vector<ListNode*> merged;
        int idx = 0, size = 0;
        ListNode* cur = nullptr;
        while (lists.size() > 1) {
            size = lists.size();
            idx = 0;
            merged.clear();
            while (idx + 1 < size) {
                cur = merge2Lists(lists[idx], lists[idx + 1]);
                merged.push_back(cur);
                idx += 2;
            }
            if (idx < size) {
                merged.push_back(lists[idx]);
            }
            lists = merged;
        }
        return lists[0];
    }

    ListNode* merge2Lists(ListNode* head1, ListNode* head2) {
        ListNode* head = nullptr, * cur = nullptr, * next = nullptr;
        while (head1 && head2) {
            if (head1->val < head2->val) {
                next = head1;
                head1 = head1->next;
            }
            else {
                next = head2;
                head2 = head2->next;
            }
            if (!head) {
                head = next;
                cur = next;
            }
            else {
                cur->next = next;
                cur = cur->next;
            }
        }
        if (cur && (head1 || head2)) {
            cur->next = head1 == nullptr ? head2 : head1;
        }
        else {
            head = head1 == nullptr ? head2 : head1;
        }
        return head;
    }
};

int main(int argc, char** argv) {
    vector<vector<int>> nums = {
        {2},
        {0},
        {-1},
    };

    vector<ListNode*> lists;
    for (int idx = 0; idx < nums.size(); idx++) {
        ListNode* nHead = nullptr, * cur = nullptr, *next = nullptr;
        for (int j = 0; j < nums[idx].size(); j++) {
            next = new ListNode(nums[idx][j]);
            if (!nHead) {
                nHead = next;
                cur = next;
            }
            else {
                cur->next = next;
                cur = cur->next;
            }
        }
        lists.push_back(nHead);
    }

    Solution solu;
    ListNode* head = solu.mergeKLists(lists);
    while (head) {
        cout << head->val << " ";
        head = head->next;
    }
    cout << endl;
    return 0;
}
```