---
title: statistical-learning-2
date: 2017-03-09 10:42:47
tags: statistical learning
categories: statistical learning
---

Chapter 3 REVIEW

<!--more-->
### 简单线性回归
$$Y=\beta _{0}+\beta _{1}X+\varepsilon $$

$$\widehat{y}=\widehat{\beta} _{0}+\widehat{\beta} _{1}x$$

$\varepsilon $的原因：

- 真实的x和y的关系可能不是线性的
- 存在测量误差
- $\varepsilon $服从$N(0,\vartheta ^{2}),\, \, \, \vartheta ^{2}=\sum\varepsilon ^{2}$
- 对$\varepsilon$的估计称为残差标准误$RSE=\sqrt{RSS/(n-2)}$



#### 残差平方和 residual sum of square 

$$RSS=e_{1}^{2}+e_{2}^{2}+...e_{n}^{2},\, \, \, e_{i}=y_{i}-\widehat{y}_{i}=y_{i}-\widehat{\beta }_{0}-\widehat{\beta }_{1}x$$

$$\therefore RSS=\sum (y_{i}-\widehat{y}_{i})^{2}=\sum (y_{i}-\widehat{\beta }_{0}-\widehat{\beta }_{1}x)^{2}$$

#### 最小二乘法求解得

$$\widehat{\beta }_{1}=\frac{\sum (x_{i}-\overline{x})(y_{i}-\overline{y})}{\sum (x_{i}-\overline{x})^{2}}=\frac{l_{xy}}{l_{xx}}$$

$$\widehat{\beta }_{0}=\overline{y}-\widehat{\beta }_{1}\overline{x}$$

定义样本均值$\widehat{\mu }$，随机变量Y总体均值$\mu$。衡量$\widehat{\mu }$对$\mu$的估计有多准确，用$\widehat{\mu }$的标准误差$SE(\widehat{\mu })$

$\widehat{\mu }$的方差$Var(\widehat{\mu })=SE(\widehat{\mu })^{2}=\frac{\vartheta ^{2}}{n}$

同理，衡量$\widehat{\beta}_{0}$和$\widehat{\beta}_{1}$分别对$\beta_{0}$和$\beta_{1}$估计的准确性，用$SE(\widehat{\beta}_{0})$和$SE(\widehat{\beta}_{1})$

$$SE(\widehat{\beta }_{0})^{2}=\vartheta ^{2}[\frac{1}{n}+\frac{\overline{x}^{2}}{\sum (x_{i}-\overline{x})^{2}}]$$

$$SE(\widehat{\beta }_{1})^{2}=\frac{\vartheta ^{2}}{\sum (x_{i}-\overline{x})^{2}}$$

#### 95%的置信区间：
该范围有95%的概率会包含未知参数的真实值。

$\beta_{1}$的95%置信区间为 $\widehat{\beta}_{1}\pm 2\cdot SE(\widehat{\beta}_{1})$

$\beta_{0}$的95%置信区间为 $\widehat{\beta}_{0}\pm 2\cdot SE(\widehat{\beta}_{0})$

#### t-statistc 和p-value确定X和Y之间是否有关系
零假设$H_{0}$: X和Y之间无关。$H_{0}: \beta _{1}=0$

备择假设$H_{a}$: X和Y之间存在一定关系  $H_{a}: \beta _{1}\neq 0$

t-statistic: 

$$t=\frac{\widehat{\beta }_{1}}{SE(\widehat{\beta }_{1})}$$

假设$H_{0}$成立，则t-statistic服从自由度n-2的t分布

定义p-value为任意观测值大于等于$\left | t \right |$的概率

如果p-value足够小，则拒绝$H_{0}$

#### 模型准确性度量
一旦拒绝了$H_{0}$，就可以讨论模型的准确性，即确定拟合质量，用两个标准

- **绝对测度：残差标准误RSE**

RSE是$\varepsilon $的估计，RSE是模型对数据失拟的绝对测度。

$$RSE=\sqrt{RSS/(n-2)}=\sqrt{\sum (y_{i}-\widehat{y}_{i})^{2}/(n-2)}$$

- **相对测度：$R^{2}$统计量**

$R^{2}$是一个比例，取值在0到1之间

$$R^{2}=\frac{TSS-RSS}{TSS}=1-\frac{RSS}{TSS}=\frac{ESS}{TSS}$$

总平方和 $TSS=\sum (y_{i}-\overline{y})^{2}$：执行回归分析前响应变量中的固有变异性

残差平方和 $RSS=\sum (y_{i}-\widehat{y}_{i})^{2}$：回归之后仍无法解释的变异性

回归平方和 $ESS=\sum (\widehat{y}_{i}-\overline{y})^{2}$：响应变量进行回归之后被消除的变异性

$$TSS=RSS+ESS$$

相关性：

$$Cor(X,Y)=\frac{\sum (x_{i}-\overline{x})(y_{i}-\overline{y})}{\sqrt{\sum (x_{i}-\overline{x})^{2}}\sqrt{\sum (y_{i}-\overline{y})^{2}}}=\frac{l_{xy}}{\sqrt{l_{xx}}\sqrt{l_{yy}}}$$

在简单线性回归中，$R^{2}=Cor(X,Y)^{2}$

### 多元线性回归
$$Y=\beta _{0}+\beta _{1}X+\beta _{2}X_{2}+...+\beta _{p}X_{p}+\varepsilon $$

$$\widehat{y}=\widehat{\beta} _{0}+\widehat{\beta} _{1}x+\widehat{\beta }_{2}x_{2}+...+\widehat{\beta }_{p}x_{p}$$

#### 残差平方和 residual sum of square 
$$RSS=\sum (y_{i}-\widehat{y}_{i})^{2}=\sum (y_{i}-\widehat{\beta }_{0}-\widehat{\beta }_{1}x-...-\widehat{\beta }_{p}x_{p})^{2}$$

#### F统计量
零假设$H_{0}$: X和Y之间无关。$H_{0}: \beta _{1}=\beta _{2}=...=\beta _{p}=0$

备择假设$H_{a}$: X和Y之间存在一定关系  $H_{a}: $至少有一个$\beta _{j}$不为0

F统计量：

$$F=\frac{(RSS_{0}-RSS)/q}{RSS/(n-p-1)}$$

当$H_{0}$成立时，F->1，F服从F分布，$E[(TSS-RSS)/p]=\vartheta ^{2}$；当$H_{a}$成立时，F>1

n比较大时，小F值即可拒绝$H_{0}$；n比较小时，需要大的F值才能拒绝$H_{0}$

#### 确定重要变量
- 向前选择 forward selection
- 向后选择 backward selection， p>n时不可用
- 混合选择 mixed selection

#### 模型拟合质量
残差标准误

$$RSE=\sqrt{RSS/(n-p-1)}$$

#### 其他问题
- 定性变量

转化为哑变量(dump variable)

- 拓展交互项

**有交互项的模型不是可加的**

实验分层原则(hierarchical principle): 模型一定含有交互项的主效应项

- 非线性关系：多项式回归
- 潜在问题
	- 数据的非线性：残差图是否有模式(U型等)
	- 误差项$\varepsilon _{i}$自相关：残差图是否有tracking的现象
	- 误差项$\varepsilon _{i}$方差不恒定：残差图是否成漏斗状(funnel shape)
	- outliers：用$studentized\, residual=\frac{e_{i}}{RSE}>3$则为outliers
	- 高杆杠点
	
	leverage statistic: 
	
	$$h_{i}=\frac{1}{n}+\frac{(x_{i}-\overline{x})^{2}}{\sum (x_{i}-\overline{x})^{2}},\, \, \, \frac{1}{n}<h_{i}<1\, \, 且\, \, \frac{1}{n}\sum h_{i}=\frac{p+1}{h}$$
	
	- collinearity：用RSS等高线图看
	
	collinearity提高，$SE(\widehat{\beta}_{i})$提高，t-statistic降低，$H_{0}$无法拒绝
	
	检验方法一：相关系数矩阵，但存在multicollinearity问题
	
	检验方法二：
	$$VIF(\widehat{\beta }_{i})=\frac{1}{1-R_{j}^{2}}$$
	
#### linear regression vs KNN
1. 参数和非参数方法优缺点
2. 如果选定的参数形式，接近$f$的真实形式，则参数法更优
3. 如果真实关系是非线性的，flexibility提高，KNN测试集MSE变化小，线性回归的test MSE提高。但在非线性情况下，KNN结果可能更差，尤其是维度大的时候。
4. 纬度灾难(curse of dimensionality)

### 补充证明
$$y=X\beta +\varepsilon $$

if $\varepsilon $ is constant variance $\vartheta ^{2}$，$\beta =(X^{T}X)^{-1}X^{T}y$

if $\varepsilon $ is non-constant variance

$$\varepsilon \sim \begin{pmatrix}
\vartheta _{1}^{2} & 0 & ... & 0\\ 
0 &  \vartheta _{2}^{2}&  ...& 0\\ 
 ...&  ...&  ...& ...\\ 
0 & 0 & ... & \vartheta _{n}^{2}
\end{pmatrix}$$

weight matrix:
$$W= \begin{pmatrix}
\frac{1}{\vartheta _{1}^{2}} & 0 & ... & 0\\ 
0 &  \frac{1}{\vartheta _{2}^{2}}&  ...& 0\\ 
 ...&  ...&  ...& ...\\ 
0 & 0 & ... & \frac{1}{\vartheta _{n}^{2}}
\end{pmatrix}$$
$$\beta =(X^{T}WX)^{-1}X^{T}Wy$$
