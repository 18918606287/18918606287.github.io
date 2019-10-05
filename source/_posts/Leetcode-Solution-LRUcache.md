---
title: 'Leetcode Solution: #146 LRUcache'
date: 2019-08-28 18:25:14
categories:
- Leetcode
tags:
- Data Structure
- Cpp
- STL
- Python
---

### Description

Design and implement a data structure for **Least Recent Used(LRU) cache**. It should support the following operations: ``get`` and ``put``.

``get(key)`` -Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.
``put(key, value)`` -Set or insert the value if the key is not already present. When the cache reached its capacity, it should invalidate the least recently used item before inserting a new item.

The cache is initialized with a **positive** capacity.

### Basic Idea

To solve this problem, we need to design a kind of data structure with the properties as follow:

1. The data structure can visit the and set/insert the item as soon as possible(such as **vector or map**).
2. The data structure can order the data **by the operation time.**
3. The data structure can quickly check for the **overflow of capacity.**

Due to the data structure in different language is not the same, I will choose **python** and **cpp** as my solution language.
<!-- more -->
### Python solution

I will introduce a kind of python data structure called **OrderedDict**. This is a kind of dictionary(in fact it is inherited from 'dictionary' of python) with the order of insertion time. Python uses an extra **circular linked list** to save the node as form $[PREV, NEXT, KEY]$ to realize the data structure. Obviously, this data structure is perfectly suitable for our problem.

The only problem we need to solve is the question is to find a data structure ordering by **operation time** but not the **insertion time**. So we just need to simply **delete and insert** every operation to solve this problem.

```Python
class LRUCache:
    import collections
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = collections.OrderedDict()

    def get(self, key: int) -> int:
        if key in self.cache:
            ret = self.cache[key]
            del self.cache[key]
            self.cache[key] = ret
            return ret
        return -1

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            del self.cache[key]

        if self.capacity == len(self.cache):
          #This for iteration is to visit the first value of cache.
            for i in self.cache.keys():
                del self.cache[i]
                break
        self.cache[key] = value
# Your LRUCache object will be instantiated and called as such:
# obj = LRUCache(capacity)
# param_1 = obj.get(key)
# obj.put(key,value)
```

### Cpp solution(using STL container)

CPP provides many kinds of STL containers, but there are nothing like the 'OrderedDict' in python. The design idea is to **combine two or more kinds of containers**(like the OrderedDict source code do). If we want to build a structure with **insertion order**, stack will be a first choice. But what we also want is to **keep the high speed of insertion and deletion** of map/vector, which will conflict with stack's properties that we can not move/delete a node in the middle of a stack. Therefore, **Linked List**(in STL is list) will be a great choice, which also matches python's choice. To keep the order of operation time, the problem we need to solve can be as follow:

1. We need a fast way to **visit/insert/delete** a node in linked list **given key**.
2. We need a fast way to **move a node to the front** of linked list after every operation.
3. When we get the first value of linked list, we need a fast way to **delete the pair in map/vector.**

These properties keeps a linked list by operation time order with short modifying time. Actually, property $1$ and $2$ can be combined due to the property of Linked List. Map can be a good way to satisfy property $1$ and $2$. We can use **map(key, node)** form to visit a linked list node quickly in $O(1)$ time. About property $3$, we need a **reverse_map(value, key)** to fast delete corresponding pair in map. We just need to delete **reverse_map[node.value]** in map. But it's not convenient and cost extra space. We can just store **node(value, key)** in linked list to do the same thing.

In conclusion, what we need is to combine a **map**(or unordered_map) and a **list.**

```cpp
class LRUCache {
public:
    LRUCache(int capacity) {
        this->cache = map<int, list<pair<int, int>>::iterator>();
        this->linkedlist = list<pair<int, int>>();
        this->cap = capacity;
    }
    int get(int key) {
        auto it = cache.find(key);
        if(it != cache.end()){
            linkedlist.splice(linkedlist.begin(), linkedlist, it->second);
            return it->second->second;
        } else return -1;
    }

    void put(int key, int value) {
        auto it = cache.find(key);
        if(it != cache.end())
            linkedlist.erase(it->second);
        linkedlist.push_front({key, value});
        cache[key] = linkedlist.begin();
        if(cache.size() > cap){
            int key_recent = linkedlist.back().first;
            cache.erase(key_recent);
            linkedlist.pop_back();
        }
    }
private:
    map<int, list<pair<int, int>>::iterator> cache;
    list<pair<int, int>> linkedlist;
    int cap;
};
```

There are also some tricky cases like we need to **check for if key in the cache first** because change a value in cache need to delete the value first and then insert a new one.

The combination of STL containers is not the only way to solve this problem. Actually many artificial data structures have better performance. A specific example is using **circular linked list nodes** just like python do in the 'OrderedDict'. I won't cover this method here and you can find related articles easily.
