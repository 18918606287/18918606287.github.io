---
title: Recover sorted tensor in Pytorch
date: 2019-11-28 08:22:07
categories:
- Pytorch
tags:
- Pytorch
---

## Problem

When I use `torch.nn.utils.rnn.pad_sequence` to Padding words and feed the padded sequence into LSTM/RNN, a input sorted by length is neccessary. But an order-changed sequence will increase the difficulty of evaluation. So here is a way to recover the sorted tensor using Pytorch functions.

## Let's go

``` python
x = torch.randn(10)
```

Here x is `tensor([-0.4321,  0.3852,  0.6008,  0.8452, -0.4709,  0.7610, -0.9743, -0.9819, -1.1142, -0.1249])` and then we do the sort.

``` python
sorted_x, idx = torch.sort(x)
```

Here idx is  the index of x, `tensor([8, 7, 6, 4, 0, 9, 1, 2, 5, 3])`. Then we can get the original order just by sorting the `idx`.

``` python
_, rev_idx = torch.sort(idx)
sorted_x[rev_idx]
```

We can see that the script prints the `tensor([-0.4321,  0.3852,  0.6008,  0.8452, -0.4709,  0.7610, -0.9743, -0.9819, -1.1142, -0.1249])` which is equals to the original `x`. It's amazing, isn't it? I'll then show you why it works.
<!-- more -->

## Mathematical Explain

We suppose there is a n-permutation corresponding to our tensor.

$$
\left(
\begin{matrix}
1,2,...,n\\
i_1,i_2,...,i_n
\end{matrix}
    \right)
$$

Then we do the sort and get a new permutation.

$$
\left(
\begin{matrix}
1,2,...,n\\
i_{j_1},i_{j_2},...,i_{j_n}
\end{matrix}
    \right), i_{j_1} \leq i_{j_2} \leq ... \leq i_{j_n}
$$

Here `idx` is corresponding to the vector $$(j_1,j_2,...,j_n)$$. Then do the second sort and get $$j_{k_1} \leq j_{k_2} \leq ... \leq j_{k_n}$$ and here `rev_idx` corresponding to vector $$(k_1,k_2,...,k_n)$$. The code `sorted_x[rev_idx]` selected elements with subscriber $$(k_1,k_2,...,k_n)$$ from the second permutaion, which means it selected the vector $$(i_{j_{k_1}}, i_{j_{k_2}},...,i_{j_{k_n}})$$.

Mention that the vector $$(j_1, j_2,...,j_n)$$ is a permutation of $$(1,2,...,n)$$. So the vector $$(j_{k_1}, j_{k_2},...,j_{k_n})$$ is also a permutation of $$(1,2,...,n)$$. **So we have that for all $$l\in (1,...,n)$$,$$j_{k_l} = l$$**. Finally, $$(i_{j_{k_1}}, i_{j_{k_2}},...,i_{j_{k_n}}) = (i_1, i_2,...,i_n)$$ which is the original tensor.
