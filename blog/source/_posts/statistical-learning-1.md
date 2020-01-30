---
title: statistical-learning-1
date: 2017-03-08 20:26:15
tags: statistical learning
categories: statistical learning
---

快速回顾学过的statistical learning知识点

<!--more-->

真实模型 $Y=f(x)+\varepsilon $

拟合模型 $\widehat{Y}=\widehat{f}(x)$

$MSE=[f(x)-\widehat{f}(x)]^{2}+Var(\varepsilon )$

- MSE: mean square error 
- $[f(x)-\widehat{f}(x)]^{2}$: reducible error
- $Var(\varepsilon )$: irreducible error

### how to estimate $f$
(1) 参数方法 parametric methods 和 非参数方法 non-parametric methods

参数方法：

- advantages: reduce the problem of estimate $f$ down to estimating a set of parameters
- disadvantage: the model we choose usually not match the true unknown form of $f$. If the chosen model is too far from the true $f$, then our estimation will be poor. If we choose a too complex model, it may cause overfitting.

非参数方法：

- advantage: by avoiding the assumption of a paticular function form of $f$, they have the potential to accuratelt fit a wider range of possible shapes for $f$.
- dis: a very large number of observations is required.

(2) 监督方法 supervised methods 和非监督方法 unsupervised methods

### 准确率 Accuracy
#### Regression

$traning MSE=\frac{1}{n}\sum_{i=1}^{n}(y_{i}-\widehat{f}(x_{i}))^{2}$
$test MSE=Ave(y_{0}-\widehat{f}(x_{0}))^{2}$

In general, as the flexibility of the statistical learining method increases, we observe a monotone decrease in the training MSE and a U-shape in the test MSE.

**Overfitting** happens because the statistical learning procedure is working too hard to find the pattern in the training fata and may pick up some patterns that are just caused by randon chance rather than by the true property of the unknown function $f$

**the bias-variance trade-off**
$test MSE=Ave(y_{0}-\widehat{f}(x_{0}))^{2}=Var(\widehat{f}(x_{0}))+[Bias(\widehat{f}(x_{0}))]^{2}+Var(\varepsilon)$

*Variance*: the amount by which $\widehat{f}$ would change if we estimated it using different training dataset. If the varaince is high, small changes in the training dataset cause large changes in $\widehat{f}$

*Bias*: the error that is introduced by approximationg a real life problem. If the true is very close to linear, the bias is small. Otherwise, the bias decreases by increasing flexibility.

**flexibility increases, variance increases but bias decreases**

![regression accuracy](https://raw.githubusercontent.com/Annashuo/hello-world/master/regression_accuracy.png)

left: common situation;  middle: the true is approximately linear;  right: the true is highly non-linear 

#### Classification

$$\left\{\begin{matrix}
I(y_{i}\neq \widehat{y}_{i})=1 \\
I(y_{i}= \widehat{y}_{i})=0
\end{matrix}\right.$$

$$training\, error\, rate=\frac{1}{n}\sum_{i=1}^{n}I(y_{i}\neq \widehat{y}_{i})$$

$$test\, error\, rate=Ave(I(y_{0}\neq \widehat{y}_{0}))$$

Bayes classifier: $Pr(Y=j|X=x_{0})$

$Bayes\, error\, rate=1-E(\underset{j}{max}Pr(Y=j|x))=E(\varepsilon )$

**The Bayes error rate is the lowest possible test error rate**