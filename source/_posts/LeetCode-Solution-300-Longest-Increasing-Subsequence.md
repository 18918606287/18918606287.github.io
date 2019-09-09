---
title: 'LeetCode Solution: #300 Longest Increasing Subsequence'
date: 2019-09-01 12:07:18
categories:
- Leetcode
tags:
- STL
- Algorithm
- Dynamic Programming
---

### Introduction
In this article I will describe two **dynamic programming** algorithms solving LIS problem and **STL functions** ``lower_bound()`` and ``upper_bound()``.

### Description
Given an unsorted array of integers, find the length of longest increasing subsequence.
Example:
>
Input: $[10,9,2,5,3,7,101,18]$
Output: 4
Explanation: The longest increasing subsequence is $[2,3,7,101]$, therefore the length is $4$.

<!-- more -->

### $O(n^2)$ Dynamic Programming Solution
Here is a trivial description that ``dp[i]`` means the length of longest increasing subsequence **with $i^{th}$ element.** Also, we can find easily that the value of ``dp[i]`` can be determined by all increasing subsequence with $j &lt; i$ that **maintain increasing property** with $i^{th}$ value. Mathematically, ``dp[i]`` is determined by all the value ``dp[j]`` with $j &lt; i$ and $nums[i] &gt; nums[j]$ which ``nums`` is the input vector. So the state transition equation is
> dp[i] = max(dp[j]) + 1 with j < i, nums[j] < nums[i]

This method need two iterations so it’s a $O(n^2)$ algorithm.
```CPP
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp(n, 0);
        int ret = 0;
        for(int i = 0; i < n; i++){
            int maximal = 0;
            for(int j = 0; j < i; j++)
                if(nums[j] < nums[i])
                    maximal = max(maximal, dp[j]);
            dp[i] = maximal + 1;
            ret = max(ret, dp[i]);
        }
        return ret;
    }
};
```

### $O(nlgn)$ Dynamic Programming Solution
Comparing all optimal subsequences with the same length, the one **with least last number** will confirm that when a new number is added in, the new subsequence will still optimal. For example, for subsequence $[1,3,5,2,7,4,5]$, we have two subsequences length $4$:
$$[1,3,5,7], [1,2,4,5]$$

Then we add $6$ into the sequence, the first subsequence is still $[1,3,5,7]$ when the second one becomes $[1,2,4,5,6]$.

But how to guarantee that the subsequence has the least last number? We can do so by replacing the number **just larger than the new number** with the new number. It’s because the replacement won’t change the length of the subsequence but will decrease the number value generally.
There is a very great property that the increasing subsequences are 'increasing', which means that given a increasing subsequence and a new number, we can find the **correct position** of the new number in the subsequence in only $O(lgn)$ time. We can generate a new largest increasing subsequence including the new number by **adding the new number** if it’s larger than all numbers in the subsequence and do replacing if not. The whole time complexity will be $O(nlgn)$.
```CPP
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp;
        int ret = 0;
        for(int i = 0; i < n; i++){
            auto it = lower_bound(dp.begin(), dp.end(), nums[i]);
            if(it == dp.end()) dp.push_back(nums[i]);
            else *it = nums[i];
        }
        return dp.size();
    }
};
```

### ``lower_bound`` and ``upper_bound`` in STL
We can mention that I use ``lower_bound`` function in the previous code. It’s a binary search function in STL. Both it ans ``upper_bound`` use binary search and return a position of a vector. The difference is that ``lower_bound`` return the position of the first number larger than **or equals to** the target and ``upper_bound`` return the position of the first number **strictly** larger than the target. There are three parameters in both functions. The first parameter is a Iterator refers to the search begin position, the second parameter is a Iterator refers to the end position and the third parameter is target number. Here is the source code of ``lower_bound``.
```CPP
template <class ForwardIterator, class T>
  ForwardIterator lower_bound (ForwardIterator first, ForwardIterator last, const T& val)
{
  ForwardIterator it;
  iterator_traits<ForwardIterator>::difference_type count, step;
  count = distance(first,last);
  while (count>0)
  {
    it = first; step=count/2; advance (it,step);
    if (*it<val) {                 // or: if (comp(*it,val)), for version (2)
      first=++it;
      count-=step+1;
    }
    else count=step;
  }
  return first;
}
```
What should be mentioned is that **the begin position will be included but the end position won’t be included.** The function uses **binary search**, so the time complexity is $O(lgn)$ where $n$ is the size between two pointers.
