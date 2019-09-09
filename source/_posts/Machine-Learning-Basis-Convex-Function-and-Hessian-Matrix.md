---
title: 'Machine Learning Basis: Convex Function and Hessian Matrix'
date: 2019-09-09 07:04:23
categories:
- Learning Notes
tags:
- Optimization
- Linear Algebra
---

# Introduction
Optimization is a focus on many kinds of machine learning algorithms like Linear Regression, SVM and K-means. But actually many kinds of target function is non-convex which means we can only find its local minima. But convex functions still plays an important role in machine learning. And Hessian Matrix is a great algebra tool to analyze convex functions since in most cases our target function will be real, continuous and $2^{nd}$-order differentiable. The main goal of this article is to record the proof of the equivalence between Convex Functions and their Hessians. Here is the some important definitions.

## Convex Set
A **Convex Set** $C\subseteq \Re^n$ is a set of points s.t. $\forall x, y \in C$ and $t \in [0,1]$, $tx+(1-t)y \in C$.

## Convex Function
A function $f:\Re^n \rightarrow \Re$ is a **Convex Function** if for $x, y \in D$, where $D$ is a **convex set**, $f$ and any $t \in [0,1]$ makes
$$
f(tx + (1-t)y) \leq tf(x) + (1-t)f(y)
$$

## Hessian Matrix
A **Hessian Matrix** is a square matrix of **second-order partial derivatives** of a function $f:\Re^n \rightarrow \Re$, usually written as:
$$
H = \nabla^2f(x) = \left[
\begin{array}{cc}
\frac{\partial^2 f}{\partial x_1\partial x_1} & \frac{\partial^2 f}{\partial x_1\partial x_2} & ... & \frac{\partial^2 f}{\partial x_1\partial x_d}\\
\frac{\partial^2 f}{\partial x_2\partial x_1} & \frac{\partial^2 f}{\partial x_2\partial x_2} & ... & \frac{\partial^2 f}{\partial x_2\partial x_d}\\
... & ... & ... & ... \\
\frac{\partial^2 f}{\partial x_d\partial x_1} & \frac{\partial^2 f}{\partial x_d\partial x_2} & ... & \frac{\partial^2 f}{\partial x_d\partial x_d}
\end{array}
\right]_{d\times d}
$$

## Positive Definite/Semi-Definite Matrix
A **real symmetric matrix** $P$ is called **Positive Semi-Definite** (PSD) when for all $x \in \Re^n$, there are $x^TPx \geq 0$. And it's called **Positive Definite** (PD) when for all $x \neq 0 \in \Re^n$, there are $x^TPx > 0$.
<!-- more -->

# The equivalence of convex function
There is a strong relationship between Convex Functions and their Hessians. Here is what I want to prove today.
> A $2^{nd}$-order differentiable function $f$ with convex domain $D$ is (strict) convex **if and only if** its Hessian is **PSD (PD)**.

This conclusion is also called the **Second Order Condition** of a convex function. To prove this, we need to introduce a **First Order Condition** that is
> A $1^{st}$-order differentiable function $f$ with convex domain $D$ is (strict) convex **if and only if** for any $x, y\in D$, $f(y) \geq f(x) + \nabla^T f(x)(y-x)$

## Proof of First Order Condition
I divided the proof into two parts. Firstly we can prove that if $f$ is a convex function, then first order condition works.
If $f$ is convex, we have
$$
f(tx+(1-t)y) = f(y+t(x-y))
$$
$$
 = f(y) + t\nabla^Tf(y)(x-y) + t^2(x-y)^T\nabla^2f(y)(x-y) + o(t\|x-y\|)
$$
$$
\leq tf(x)+(1-t)f(y).
$$
So, we can see
$$
tf(x) \geq tf(y) + t\nabla^Tf(y)(x-y) + t^2(x-y)^T\nabla^2f(y)(x-y) + o(t\|x-y\|)
$$
$$
f(x) \geq f(y) + \nabla^Tf(y)(x-y) + t(x-y)^T\nabla^2f(y)(x-y) + \frac{o(t\|x-y\|)}{t}
$$
Let $t\rightarrow 0$,
$$
f(x) \geq f(y) + \nabla^Tf(y)(x-y)
$$

Then we can prove that, under the case of first order condition, $f$ is a convex function.
If $f$ satisfy the first order condition, for all $x, y\in \Re^n$ and $t\in [0,1]$, we have
$$f(x) \geq f(tx+(1-t)y) + \nabla^Tf(tx+(1-t)y)(x-(tx+(1-t)y)$$
$$f(y) \geq f(tx+(1-t)y) + \nabla^Tf(tx+(1-t)y)(y-(tx+(1-t)y)$$
Add them together, we have
$$
tf(x)+(1-t)f(y)
$$
$$
\geq (t+(1-t))f(tx+(1-t)y) + [tx+(1-t)y-(t+1-t)(tx+(1-t)y)]\nabla^Tf(tx+(1-t)y)
$$
$$
= f(tx+(1-t)y) + 0 = f(tx+(1-t)y)
$$
So $f(x)$ is a convex function.

## Proof of Second Order Condition
Now all prerequisites are proved, it's turn to prove the *Second Order Condition*! Also, I depart the proof into two parts.
First we prove that if the Hessian of $f$, $H$ is $PSD$, then $f$ is convex.

If $f$ is PSD, there exists $\xi$ that
$$
f(x) = f(y) + \nabla^Tf(y)(y-x) + \frac{1}{2}(y-x)^T\nabla^2f(\xi)(y-x) \geq f(y) + \nabla^Tf(y)(y-x)
$$
So $f$ is convex due to the **first order condition**.

Then we can prove the reverse part.
If $f$ is convex, according to the **first order condition**, we suppose for all $y$,
$$
f(x+\lambda y) = f(x) + \lambda\nabla^Tf(x)y + \frac{1}{2}\lambda^2y^T\nabla^2f(x)y + o(\lambda^2\|y\|^2)
$$
$$
\geq f(x) + \nabla^Tf(x)(x+\lambda y - x)
$$
Then,
$$
\frac{1}{2}\lambda^2y^T\nabla^2f(x)y + o(\lambda^2\|y\|^2) \geq 0
$$
$$
\Rightarrow y^T\nabla^2f(x)y + \frac{o(\lambda^2\|y\|^2)}{\frac{1}{2}\lambda^2} \geq 0
$$
Let $\lambda\rightarrow0$, we have $y^T\nabla^2f(x)y \geq 0$
So $\nabla^2f(x)$ is PSD.
