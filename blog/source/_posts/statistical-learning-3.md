---
title: statistical-learning-3
date: 2017-03-09 18:51:51
tags: statistical learning
categories: statistical learning
---

Chapter 4 REVIEW

<!--more-->

### Logistic Regression

logistic function:

 $$p(x)=\frac{e^{\beta _{0}+\beta _{1}x}}{1+e^{\beta _{0}+\beta _{1}x}}$$
 
odds:(odd>0)
 
$$\frac{p(x)}{1-p(x)}=e^{\beta _{0}+\beta _{1}x}$$

logit:

$$log(\frac{p(x)}{1-p(x)})=\beta _{0}+\beta _{1}x$$

likelihood function:

$$
\begin{equation}
\begin{aligned}
l(\beta _{0},\beta _{1})&=\prod_{i=1}^{n}p(Y=y _{i}|X=x _{i})\\
&=\prod_{i=1}^{n}p(x_{i})^{y _{i}}(1-p(x _{i}))^{1-y_{i}}\\
&=\prod_{i;y _{i}=1}p(x _{i})\prod_{i;y _{i}=0}(1-p(x _{i}))
\end{aligned}
\end{equation}
$$

$$
\begin{equation}
\begin{aligned}
log(l(\beta _{0},\beta _{1}))&=\sum_{i=1}^{n}log(1-p(x_{i}))+\sum_{i=1}^{n}y_{i}log\frac{p(x)}{1-p(x)}\\
&=-\sum_{i=1}^{n}log(1+e^{\beta _{0}+\beta _{1}x})+\sum_{i=1}^{n}y_{i}(\beta _{0}+\beta _{1}x)
\end{aligned}
\end{equation}
$$

### Multiple Logistic Regression
logit:

$$log(\frac{p(x)}{1-p(x)})=\beta _{0}+\beta _{1}x_{1}+...+\beta _{p}x_{p}$$

$$p(x)=\frac{e^{\beta _{0}+\beta _{1}x_{1}+...+\beta _{p}x_{p}}}{1+e^{\beta _{0}+\beta _{1}x_{1}+...+\beta _{p}x_{p}}}$$

### Bayes' Theorem
density function:

$$f_{k}(x)=P_{r}(X=x|Y=k)$$

posterior probability:

$$p_{k}(x)=P_{r}(Y=k|X=x)=\frac{\pi _{k}f_{k}(x)}{\sum_{l=1}^{k}\pi _{l}f_{l}(x)}=\frac{P_{r}(Y=k)P_{r}(X=x|Y=k)}{\sum_{l=1}^{k}(Y=l)P_{r}(X=x|Y=l)}$$