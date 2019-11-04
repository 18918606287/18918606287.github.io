---
title: 'Machine Learning Basis: Lagrange Duality and KKT Condition'
date: 2019-11-04 04:32:23
categories:
- Learning Notes
tags:
- Optimization
---

## Introduction

Duality and KKT condition are very important for machine learning, especially in SVM models. I'll focus more on the high-level idea and the derivation of Lagrange Duality and how to introduce to KKT condition. There are some connceptions should be covered first.

### Optimization Problems without Restrictions

The basic form of optimization problem without restrictions is just like finding the $x \in \Re^d$ makes that
$$
min_{x\in \Re^d} f(x)
$$

A simple solution is to calculate the derivatives of $f(x)$, solve the equation $$f'(x^*) = 0$$ and test if $$x^*$$ is the minimal value.
<!-- more -->

### Lagrange Multiplier

Consider an Optimization Problem with equality restrictions.
$$
min_{x\in \Re^d} f(x)
$$
$$
s.t. h_i(x) = 0
$$

Lagrange Multiplier is a method to solve this kind of problem. We can rewrite the objective function as $$f(x) + \sum^n_{i=1}\lambda_i h_i(x)$$. Then we can prove that the solution of $$min_{x\in \Re^d,\lambda_i\in \Re }f(x) + \sum^n_{i=1}\lambda_i h_i(x)$$ is equal to the solution of previous problem. Here $$\lambda_i$$ are called the Lagrange Multiplier. The new optimization problem is
$$
min_{x\in \Re^d} f(x) + \sum^n_{i=1}\lambda_i h_i(x)
$$

And here the new function $$\mathcal{L}(x,\lambda) = f(x) + \sum^n_{i=1}\lambda_i h_i(x)$$ is called Lagrange function.

If there is any $$h_i(x) \neq0$$, the minimum will become $-\infty$ due to the unrestricted $$\lambda_i$$, so we should add a restriction that $$\nabla_{x,\lambda}\mathcal{L}(x, \lambda)= 0$$ which makes the solution finite and converge into $$h_i(x) = 0$$.

### Dual Problem

Let $$\mathcal{L}(x,\lambda, \mu) = f(x) + \sum_{i=1}^n\lambda_i h_i(x) + \sum_{j=1}^m\mu_j g_j(x)$$,then there is a trivial theorem that
$$
d^* = max_{\lambda, \mu}(min_{x}(\mathcal{L}(x, \lambda,\mu))) \leq min_{x}({max_{\lambda, \mu}\mathcal{L}(x, \lambda, \mu)}) = p^*
$$
Here $$d^* = max_{\lambda, \mu}(min_{x}(\mathcal{L}(x, \lambda,\mu)))$$ is the dual problem of $$p^*$$.

## Derivation

### Transformation of Primal Problem

Assume $$f(x), g_i(x), h_j(x)$$ are continuous functions on $$\Re^d$$, then consider the restricted optimization problem.
$$
min_{x\in \Re^d} f(x)
$$  
$$
s.t.\left\{
    \begin{array}{lr}
        g_i(x) \leq 0\\
        h_i(x) = 0
    \end{array}
\right.
$$

We have already known that the primal problem without restrictions can be solved easily by calculating derivatives and testing. So our first step is to translate the primal problem into a problem without restrictions.

We have a enhanced Lagrange Function in form of $$\mathcal{L}(x,\lambda, \mu) = f(x) + \sum_{i=1}^n\lambda_i h_i(x) + \sum_{j=1}^m\mu_j g_j(x)$$. Here $$\mu_j \geq 0$$ because **the direction** of $$g_j$$ has been restricted.

Define a new function $$d(x) = max_{\lambda,\mu>0}\mathcal{L}(x, \lambda, \mu) $$, we can conclude that $$min_{x}d(x) = min_{x}f(x)$$ under all primal constraints.

Obviously, $$d(x) \geq max_{\lambda, \mu}f(x) = f(x)$$, so $$d(x)$$ is an upper bound of $$f(x)$$. Then under all constraints, we have $$\sum_{i=1}^n\lambda_i h_i(x) + \sum_{j=1}^m\mu_j g_j(x) = 0$$, then $$d(x) = max_{\lambda, \mu}f(x) = f(x)$$.

In conclusion, the primal problem has an equivalent form
$$
min_{x\in \Re^d}({max_{\lambda, \mu}\mathcal{L}(x, \lambda, \mu)})
$$
$$
s.t.\left\{
    \begin{array}{lr}
        g_i(x) \leq 0\\
        h_i(x) = 0
    \end{array}
\right.
$$

### KKT condition

We have already known the equivalent form of primal problem, but in this form we should still consider the constraints which makes the calculation too complicated. The next step is to find a simpler way of finding the **best solution**.

Consider the dual problem, a well property should be $$d^* = p^*$$ when $$x = x^*$$ is the best solution of primal problem.

Think back the transformation of primal problem, if dual problem is equal to the primal problem on $x = x^*$, the formula should be
$$
min_{x}(max_{\lambda, \mu} f(x) + \sum_{i=1}^n\lambda_i h(x) + \sum_{i=1}^m\mu_j g_j(x))
$$
$$ = max_{\lambda, \mu}(min_{x}f(x) + \sum_{i=1}^n\lambda_i h(x) + \sum_{i=1}^m\mu_j g_j(x)))
$$
$$
s.t.\mu_j \geq 0
$$

Then consider the Lagrange condition of both inner optimizations which are $$max_{\lambda, \mu}\mathcal{L}(x,\lambda,\mu)$$ and $$min_{x}\mathcal{L}(x,\lambda,\mu)$$. This leads to $$\nabla_x\mathcal{L}(x^*) = 0$$ and $$\nabla_\lambda\mathcal{L} = 0$$.

Then consider the parameter $$\mu_j$$. There are two situations about $$g_j(x)$$. First is that the minimized point is of $$g_j(x) = 0$$ and the other is that the minimized point is of $$g_j(x) < 0$$.

For the first case, the inequality constraint becomes a equality constraint. That is
$$
g_j(x) = 0
$$

For the second case, the inequality constraint disappears, that is
$$
\mu_j = 0
$$

So combine two situations together we have $$\mu_j g_j(x)=0$$. Then under this constraint, the $$\mathcal{L}$$ becomes a regular Lagrange Function, which leads to a Lagrange Multiplier constraint that
$$
\nabla \mathcal{L}_{x, \lambda} = 0
$$

So the final constraint becomes
$$
\nabla f(x^*) + \sum_{i=1}^n \lambda_i\nabla h_i(x^*) + \sum_{j=1}^m \lambda_j\nabla g_j(x^*) = 0
$$
$$
h_i(x^*) = 0
$$
$$
\mu_j g_j(x^*) = 0
$$
$$
\mu_j \geq 0
$$

This is the KKT condition.
