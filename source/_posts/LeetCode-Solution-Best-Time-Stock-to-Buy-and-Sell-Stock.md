---
title: 'LeetCode Solution: Best Time to Buy and Sell Stock'
date: 2019-09-05 07:13:50
categories:
- Leetcode
tags:
- Dynamic Programming
- Algorithm
---

### Introduction
In this article I will try to solve **Best Time to Buy and Sell Stock** series problem, including **Best Time to Buy and Sell Stock I, II, III, IV** and **with Cooldown.** Most of them are solved by **dynamic programming** and I will focus on construct transition equation and dimension reduction.  

### Description
The description of **Best Time to Buy and Sell Stock I** is:

Say you have an array for which the $i^{th}$ element is the price of a given stock on day $i$.

If you were only permitted to complete at most one transaction (i.e., buy one and sell one share of the stock), design an algorithm to find the maximum profit.

Note that you cannot sell a stock before you buy one.

Example:
```
Input: [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
```

<!-- more -->
### Solution of Problem I
A simple idea is using ``dp[i]`` as **the most profit buying in $i^{th}$ day.** Then the transition equation will be ``dp[i] = max(prices[j] - prices[i]) for all j > i`` and the soluton is ``max(dp)``. It will be an $O(n^2)$ algorithm. But there is a waste of computation in this method. We suppose $j$ is the specific day that  ``dp[i] = prices[j] - prices[i]``, then if there is a $k$ makes $dp[k] < dp[i]$ and $k > i$, then we have

$$
dp[k] = max(prices[l] - prices[i]) > prices[j] - prices[k] > dp[i].
$$

So the soluton won't be ``dp[i]``. Under this circumstance, we cam simplify our algorithm by **always searching lower price day as buying day**, record the current price minus buying day price(**the lowest price before/on current $i^{th}$ day**) and generate a sequence of profit. The ``profit[i]`` means the difference between $i^{th}$ day price and the lowest price before/on $i^{th}$ day. So ``max(profit)`` will be the solution. By doing so, we reduce the method into $O(n)$ time. Here is the cpp code.

```CPP
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.empty()) return 0;
        int buy = prices[0];
        int maxPro = 0;
        for(int price:prices){
            if(price < buy){
                buy = price;
            }
            maxPro = max(maxPro, price-buy);
        }
        return maxPro;
    }
};
```

### Solution of Problem II
In problem II, we have not the transaction number limitation, **we can buy/sell any times.** When we try to using the ``dp[i]`` as above, we find that it's hard to build a transition equation because we don't know how many transaction times there will be. We have to change our state description. We have **only three actions** in a day, buying, selling and doing nothing, so we can use two states to describe a day, i.e. **a day with stock** and **a day without stock**. Let ``nohold[i]`` be the maximal profit when we have not stock in $i^{th}$ day, ``hold[i]`` be the maximal profit when we have stock. Then the transition equation will be

```CPP
hold[i] = max(hold[i-1], nohold[i-1] - prices[i]);
nohold[i] = max(hold[i-1] + prices[i], nohold[i-1]);
```

That simply means if we have stock in $i^{th}$ day, the stock can be bought today or we already have it yesterday and if we have stock in $i^{th}$ day, the stock can be sold today or yesterday or before. By this equation, we can solve this problem in one pass. Don't forget the initialization ``nohold[0] = -prices[0]``.

```CPP
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.empty()) return 0;
        vector<int> nohold(prices.size(), 0);
        vector<int> hold(prices.size(), 0);
        hold[0] = -prices[0];
        for(int i = 1; i < prices.size(); i++){
            hold[i] = max(hold[i-1], nohold[i-1] - prices[i]);
            nohold[i] = max(hold[i-1] + prices[i], nohold[i-1]);
        }
        return nohold[nohold.size()-1];
    }
};
```

There is another solution **do not use DP.** A trivial idea is that we buy all the stock at the begin of an **increasing line** and sell it at the end of line, we can get the most profit.

```CPP
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int maximal = 0;
        for(int i = 1; i < prices.size(); i++){
            if(prices[i] > prices[i-1]) maximal += prices[i] - prices[i-1];
        }
        return maximal;
    }
};
```

### Soluton of III & IV
Problem III is a special case of Problem IV, so we just introduce Problem IV. In Problem IV, we have a limitation that **we can only buy $k$ times($k$ is given).** It can be solved simply like the DP algorithm of Problem II. We can use similar state description and just increase a dimension of **transaction times.** Let ``hold[i][j]`` as the maximal profit when we have stock and $j$ transitions on $i^{th}$ day and ``nohold[i][j]`` as the maximal profit when we have no stock and have  $j$ transitions on $i^{th}$ day. Also like Problem II, the transition equation can be written as

```cpp
hold[i][j] = max(hold[i-1][j], nohold[i-1][j-1] - prices[i]);
nohold[i][j] = max(nohold[i-1][j], hold[i-1][j] + prices[i]);
```

The solution will be ``nohold[n-1][k-1]``. What need to be mentioned is that **we counting transaction by counting buying numbers but not selling.** Then it's a one-pass method.

```CPP
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        if(prices.empty() || k==0) return 0;
        int n = prices.size();
        vector<vector<int>> hold(n, vector<int>(k, 0)), nohold(n, vector<int>(k, 0));
        //hold[0][0] = -prices[0];
        for(int i = 0; i < n; i++){
            for(int j = 0; j < k; j++){
                if(i > 0){
                    if(j > 0)
                        hold[i][j] = max(hold[i-1][j], nohold[i-1][j-1] - prices[i]);
                    else
                        hold[i][j] = max(hold[i-1][j], -prices[i]);
                    nohold[i][j] = max(nohold[i-1][j], hold[i-1][j] + prices[i]);
                }
                else hold[i][j] = -prices[i];
            }
        }
        return nohold[n-1][k-1];
    }
};
```

But the code **did not pass!** We got a **Memory Limit Exceeded.** So I start to reduce the dimension of the equation. Obviously, both ``hold[i][j]`` and ``nohold[i][j]`` have only relationship with ``hold[i-1][*]`` and ``nohold[i-1][*]``. So we can just reduce it as

```cpp
hold[j] = max(hold[j], nohold[j-1] - prices[i]);
nohold[j] = max(nohold[j], hold[j] + prices[i]);
```

Also, using a **sentinel $0$ in nohold[j]** can make code looks better(reduce the number of $if$). So we get the code like this.

```CPP
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        if(prices.empty() || k==0) return 0;
        int n = prices.size();
        vector<int> hold(k, INT_MIN), nohold(k+1, 0);
        hold[0] = -prices[0];
        for(int i = 0; i < n; i++){
            for(int j = 0; j < k; j++){
                    hold[j] = max(hold[j], nohold[j] - prices[i]);
                    nohold[j+1] = max(nohold[j+1], hold[j] + prices[i]);
            }
        }
        return nohold[k];
    }
};
```

~Ok, we have already solved it!~ Wait, it's still **Memory Limit Exceeded!** But why? If we consider a super large $k$ that the limitation is meaningless to the problem, the problem **reduces into Problem II.** But the time complexity of our solution will still be $O(k*\dot n)$, which is a super large number especially comparing with $O(n)$ solution in Problem II. We can solve this by a simple $if$ sentence.

```CPP
if(k > n/2){
  int res = 0;
  for(int i = 1; i < prices.size(); i++)
    res = max(res, res + prices[i]-prices[i-1]);
  return res;
}
```

And here is the whole program of Problem IV.

```CPP
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        if(prices.empty() || k==0) return 0;
        int n = prices.size();
        if(k > n/2){
            int res = 0;
            for(int i = 1; i < prices.size(); i++)
                res = max(res, res + prices[i]-prices[i-1]);
            return res;
        }

        vector<int> hold(k, INT_MIN), nohold(k+1, 0);
        hold[0] = -prices[0];
        for(int i = 0; i < n; i++){
            for(int j = 0; j < k; j++){
                    hold[j] = max(hold[j], nohold[j] - prices[i]);
                    nohold[j+1] = max(nohold[j+1], hold[j] + prices[i]);
            }
        }
        return nohold[k];
    }
};
```

### Solution of Problem **with Cooldown**
Cooldown means we have to ~have relax and take a coffee~ the day after selling. **Buying the day after a selling is not allowed.** That means our states description above can not be used again...Of course not! We can just do a little modification, adding a new vector called ``cooldown[i]`` means the maximal profit when we **just sell or do nothing** on the $i^{th}$ day. We have ``have_stock[i]`` and ``have_no_stock[i]`` as above. We can find the transition of cooldown like ``cooldown[i] = max(hold_no_stock[i-1], hold_stock[i-1] + prices[i])`` which means today we sell the stock or do nothing. The transition of hold_stock is still ``hold_stock[i] = max(hold_stock[i-1], hold_no_stock[i-1] - prices[i])`` because cooldown doesn't influence buying. Finally the transition equation pf ``hold_no_stock[i]`` can be ``hold_no_stock[i] = max(hold_no_stock[i-1], cooldown[i-1])``, meaning that today is a **cooldown day or no stock day.** Combine them together we have_stock

```CPP
hold_no_stock[i] = max(hold_no_stock[i-1], cooldown[i-1]);
hold_stock[i] = max(hold_stock[i-1], hold_no_stock[i-1] - prices[i]);
cooldown[i] = max(hold_no_stock[i-1], hold_stock[i-1] + prices[i]);
```

Don't forget the initialization ``hold_stock[0] = -prices[0]; cooldown[0] = INT_MIN;``. It's also a $O(n)$ one-pass method now. In conclusion, all these kind of problem can be solved by dynamic programming idea and the basic idea is to form transition equation. **The number of variables or the number of dimensions are equivalent** in constructing equation. So if you have not idea how to form the equation, including the variable number of state will be a good choice.

```CPP
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.empty()) return 0;
        int n = prices.size();
        vector<int> hold_stock(n, 0), hold_no_stock(n, 0), cooldown(n, 0);
        hold_stock[0] = -prices[0];
        cooldown[0] = INT_MIN;
        for(int i = 1; i < n; i++){
            hold_no_stock[i] = max(hold_no_stock[i-1], cooldown[i-1]);
            hold_stock[i] = max(hold_stock[i-1], hold_no_stock[i-1] - prices[i]);
            cooldown[i] = max(hold_no_stock[i-1], hold_stock[i-1] + prices[i]);
        }
        return max(cooldown[cooldown.size()-1], hold_no_stock[hold_no_stock.size()-1]);
    }
};
```
