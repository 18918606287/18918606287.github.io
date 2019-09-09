---
title: 'Leetcode Solution: #142 Linked List Circle'
date: 2019-08-27 14:07:05
categories:
- Leetcode
tags:
- Algorithm
---

### Description
Given a linked list and return where the circle begins. For example, a linked list $[3, 2, 0, 4]$ having circle $[2, 0, 4]$ is shown below.

![Linked_circle](https://raw.githubusercontent.com/18918606287/picture4blog/master/linked_circle.png?token=AKSH2L6SAQWW7JM72BLNEZC5N3VDA)

The algorithm should return the second node. I use C++ to solve this problem and define the node as below.

```cpp
//Definition for singly-linked list.
struct ListNode {
     int val;
     ListNode *next;
     ListNode(int x) : val(x), next(NULL) {}
};
```
<!-- more -->
### Solution 1: hashset

A trivial idea to solve this problem is saving the node information in a hashset when traversing and find if there is a visited node.
```cpp
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode *cur = head;
        unordered_set<ListNode*> storage;
        while(cur!=NULL){
            if (storage.find(cur)!=storage.end()){
                return cur;
            }
            storage.insert(cur);
            cur = cur->next;
        }
        return NULL;
    }
};
```

This is a method with O(n) time complexity and O(n) space complexity. But if there is a method to solve this problem with O(1) space complexity?

### Solution 2: fast and slow pointers

Set two pointers which slower one move one time one step and faster one move one time two steps. Once they meet, **reset the faster one to the head pointer**, then finally they will meet in the begin node of circle. This is an algorithm without extra space. But why it works?

There is a mathematical idea. Suppose the distance from head node to the begin of circle is $x_1$, the distance from begin of circle and meeting point on the circle is $x_2$, the distance from meeting point back to the begin of the circle is $x_3$. Then there is the velocity equation.
$$2*((x_1 + x_2) + k_1(x_2+x_3)) = (x_1 + x_2) + k_2 (x_2+x_3)$$
$$-> x_1 + x_2 = (k_2 - 2*k_1 - 1)(x_2 + x_3)$$
$$-> (x_2 + x_3)|(x_1 + x_2)$$

It means that the difference between $x_3$ and $x_1$ is the multiple of circle length. Due to the definition of $x_3$ and $x_1$, if the fast pointer move from the head node when the slow pointer move from the meeting point, **finally the slow pointer and fast pointer will meet on the begin of the circle.**

```cpp
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        if(head == NULL || head->next == NULL || head->next->next == NULL) return NULL;
        ListNode* fast = head->next->next;
        ListNode* slow = head->next;
        while(fast != slow){
            if(fast->next == NULL || fast->next->next == NULL) return NULL;
            fast = fast->next->next;
            slow = slow->next;
        }
        fast = head;
        while(fast != slow){
            fast = fast->next;
            slow = slow->next;
        }
        return slow;
    }
};
```

Don't forget the special cases of NULL pointer and if fast pointer move to NULL means **there is no circle.**
