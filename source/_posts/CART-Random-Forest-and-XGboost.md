---
title: 'CART, Gradient Boosting and XGboost'
date: 2020-01-02 03:10:42
categories:
- Learning Notes
tags:
- Machine Learning
---

## Classification And Regression Tree

Classification And Regression Tree(CART) is a kind of decision tree model can be used both for classification and regression. CART models are always binary trees. 

### Classification Tree

The main process of a classification tree is shown below.

1. Choose a variable $x_i$ and a ***split point*** $v_i$, then split the data space into two parts. All data in the first part satisfy that $x_i \leq v_i$ and all data in the second part satisfy that $x_i > v_i$. For discrete data, the condition is equivalent to $x_i = v_i$ and $x_i \neq v_i$.
2. Split the space recursively until the stopping condition.
3. Stopping condition is that all data in this subspace is in the same class. There are also some other conditions like using $\chi^2$ value or other independence test and stop the spliting when are splited data are independent.

A question is how to choose the split point. In classification task, Gini impurity is widely used. The Gini impurity can be simply understand as ***the probability of misclassified***.

$$
Gini(p) = \sum_{k = 1}^mp_k(1-p_k) = 1-\sum_{k = 1}^mp_k^2
$$

Under this situation, $p_k = \frac{|C_k|}{|D|}$ where $C_k$ is the subset of $D$ with data labeled as $k^{th}$ class.

If $D_1 = \{X|x_i \leq v_i\}$, $D_2 = \{X|x_i > v_i\}$, we have $D_1\cup D_2 = D$ and $D_1\cap D_2 = \emptyset$, the gain of gini impurity shown below.

$$
Gain(D, x_i) = \sum_{j=1}^2\frac{|D_j|}{|D|}Gini(D_j)
$$

Here, the smaller gain of gini is, the less misclassification. So we always choose the split and $x_i$ makes the gain of gini smallest. ***CART will combine catagories into two super-catagories before spliting if there are more than two catagories***.

<!--more -->
### Regression Tree

The main process of a regression tree is most likely the classification tree. There are several difference between them.

1. Fit the residual of previous regression results to the labels and add them together.
2. Usually use inner-class minimal mean squared error instead of Gini impurity as measurement.

CART choose the best spliting point to optimize the following optimization problem.

$$
min_{j,s}[min\sum_{x_i\in R_1(j,s)}(y_i-c_1)^2 + min\sum_{x_i\in R_2(j,s)}(y_i-c_2)^2]
$$

Here $R_i(j,s)$'s are the subspace after spliting by condition $(j,s)$. $x_j$ is the spliting feature and $s$ is the spliting point. We use this equation in stead of Gini impurity because we want to minimize the inner-class distances.

Then we can get $M$ subspaces and for each subspace $R_m$, we ***calculate the mean value as the regression value***. i.e. $\hat c_m = \frac{1}{N_m}\sum_{x_i\in R_m} y_i$.

The final regression function is shown below.

$$
f(x) = \sum_{m=1}^M \hat c_m I(x\in R_m)
$$

Then we can use mean square error to evaluate the tree and fit the residuals to improve the model. It's a simple ***boosting method***. Let $T_i(x)$ be the estimation of $y - f_{i-1}(x)$ based on CART. then we have $f_i(x) = f_{i-1}(x) + T_i(x)$. It's a special case of Gradient Boosting Decision Tree(***The loss function is mean square error***).

## Gradient Boosting Decision Tree

The boosting strategy mentioned above has a more general form. Gradient boosting Decision Tree(GBDT) use a similar recursive formula.

$$
F_m(x) = F_{m-1}(x) + argmin_{h\in H}\sum_{i=1}^nLoss(y_i,F_{m-1}(x_i) + h(x_i))
$$

We can treat the loss function as a function of vector $F_{m-1}(x)$. Then using ***gradient descent method***, the $F_{m}(x)$ can be calculated by $F_{m-1}(x) - \eta\nabla_{F_{m-1}} Loss(x)$. We use $F_{m-1}(x)$ instead of $x$ as gradient variable because we can not get a expression of $x$ from Decision Tree model. Then our target is to find a way to calculate $\nabla_{F_{m-1}}Loss(x)$ or a ***reasonable estimation***.

Also using CART, if we use $\{x_i,-\frac{\partial Loss(y_i,F_{m-1}(x_i))}{\partial F_{m-1}}\}$ to build a CART $T_m(x)$ and it's a estimation of $-\nabla_{F_{m-1}}Loss(x)$.

### Classification

The classification tree is not same as CART because the CART classification tree does not have gradient. The way of classification is using ***log-odds value***. Just like logistic regression or neural network classification, we first estimate a continuous value $logit = ln\frac{P(y=1|x)}{P(y=0|x)}$ and use sigmoid function(or softmax in high-dimension) to translate it into probability. Just like the logistic regression, we can use ***cross entropy*** as out loss function. Here we use binary classification as an example.

$$
loss(x_i,y_i) = -y_ilog\hat y_i - (1-y_i)log(1-\hat y_i)
$$

The function of probability is shown below.

$$
P(y=1|x) = \frac{1}{1 + e^{-F_{m-1}(x)}}
$$

So we can get

$$
loss(y_i, F_{m-1}(x_i)) = y_ilog(1+e^{-F_{m-1}(x_i)}) + (1-y_i)[F_{m-1}(x_i) + log(1+e^{-F_{m-1}(x_i)})]
$$

Then calculate the gradient,

$$
-\frac{loss}{F_{m-1}}(x_i,y_i) = y_i - \hat y_i
$$

If we have $k$ labels, we need to use one-hot encoding and softmax function then ***fit $k$ trees each iteration*** to fit each dimension.

To fit the model better, there is a variation.

$$F_{m-1}(x) + \eta\rho_mT_m(x_i)$$

where $\rho_m$ is the result of linear search $argmin_\rho\sum_{i}loss(x_i,y_i|F_{m-1(x_i)}+ \rho T_m(x_i))$ and $\eta$ is learning rate.

## XgBoost

In XgBoost, the regression results are represented by the formula

$$
\hat y = \sum_{i = 1}^K f_k(x)
$$

Here every $f_k(x)$ is a regression tree structure. Assume the tree has $T$ leaves, $q(x)\in \{1,2,...,T\}$ is the leave of $x$ and $f_k(x) = w_{q(x)}$ is the score of the input.

### Regularization

The regularization of XgBoost has two parts, the complexity of tree and the scalability of scores.

$$
\Omega(f_t(x)) = \gamma T + \frac{1}{2}\lambda \|w\|^2
$$

Here $T$ is the number of leaves in the tree and $w \in \Re^T$ is the score of leaves.

### Splitting Choice

XgBoost uses second order Taylor Expansion to approach the true value of loss function. Assume loss function is $l(y_i,\hat y_i)$, we have $l(y_i,\hat y_i^{(t-1)} + f_k(x_i)) = l(y_i) + g_if_k(x_i) + \frac{1}{2}h_if_k^2(x_i)$ where
$$
g_i = \frac{\partial l(y_i, \hat y^{(t-1)}_i)}{\partial \hat y^{(t-1)}_i}
$$
$$
h_i = \frac{\partial^2 l(y_i, \hat y^{(t-1)}_i)}{\partial (\hat y^{(t-1)}_i)^2}
$$

Remove the constant term, we have the objective function.

$$
\mathcal{L}^{(t)} = \sum_{i=1}^n[g_if_t(x_i) + \frac{1}{2}h_if_t^2(x_i)] + \gamma T + \frac{1}{2}\lambda \|w\|^2
$$

Rewrite the function, we have

$$
\mathcal{L}^{(t)} = \sum_{j=1}^T[w_j\sum_{i\in I_j}g_i + \frac{1}{2}w_j^2(\sum_{i\in I_j}h_i + \lambda)] + \gamma T.
$$

where $I_j = \{i|q(x_i) = j\}$. Then calculate the derivatives and zero point.

$$
\frac{\partial \mathcal{L}^{(t)}}{\partial w_j} = [\sum_{i\in I_j}g_i + w_j(\sum_{i\in I_j}h_i + \lambda)] = 0
$$

We can get the optimal score by solving the equation above.
$$
w_j^* = -\frac{\sum_{i\in I_j}g_i}{\sum_{i\in I_j}h_i + \lambda}
$$

Then bring it back, we have the optimal objective function.
$$
\mathcal{L}^{(t)} = -\frac{1}{2}\sum_{j=1}^T\frac{(\sum_{i\in I_j}g_i)^2}{\sum_{i\in I_j}h_i+\lambda} + \gamma T.
$$

The smaller the $\mathcal{L}$ is, the better the tree structure is, so we can choose the splitting point make the $\mathcal{L}$ smallest.

The idea is to choose a splitting point making the following value as large as possible.

$$
\mathcal L_{split} = \mathcal L_{Ori} - \mathcal L_{L} - \mathcal L_{R} = \frac{1}{2}[\frac{(\sum_{i\in I_L}g_i)^2}{\sum_{i\in I_L}h_i+\lambda} + \frac{(\sum_{i\in I_R}g_i)^2}{\sum_{i\in I_R}h_i+\lambda} - \frac{(\sum_{i\in I}g_i)^2}{\sum_{i\in I}h_i+\lambda}] - \gamma
$$

The algorithm will stop creating subtrees when $\mathcal{L}_{split} < 0$ or reach the maximal depth.