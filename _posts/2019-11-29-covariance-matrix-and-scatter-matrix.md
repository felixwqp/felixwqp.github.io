---
layout: post
title: "Understanding about covariance and scatter matrix"
description: "Naive Understanding to it."
comments: true
keywords: "dummy content, statistic, matrix, covariance"
---




### 3. 协方差（Covariance）
同向变化的程度
[zhihu](https://www.zhihu.com/question/20852004/answer/134902061)
$$
\operatorname{Cov}(X, Y)=E\left[\left(X-\mu_{x}\right)\left(Y-\mu_{y}\right)\right]
$$
相关系数: 
$$
\rho=\frac{\operatorname{Cov}(X, Y)}{\sigma_{X} \sigma_{Y}}
$$

翻译一下：就是用X、Y的协方差除以X的标准差和Y的标准差。

所以，相关系数也可以看成协方差：一种剔除了两个变量量纲影响、标准化后的特殊协方差
它消除了两个变量变化幅度的影响，而只是单纯反应两个变量每单位变化时的相似程度。


$$
\sigma_{X}=\sqrt{E\left(\left(X-\mu_{x}\right)^{2}\right)}
$$
“变量值与变量均值之差”$X-\mu_{x}$是什么呢？就是偏离均值的幅度：
这样在后面求平均时，每一项数值才不会被正负抵消掉，最后求出的平均值才能更好的体现出每次变化偏离均值的情况。
当然，最后求出平均值后并没有结束，因为刚才为了消除负号，把$X-\mu_{x}$进行了平方，那最后肯定要把求出的均值开方，将这个偏离均值的幅度还原回原来的量级。于是就有了下面标准差的公式：



协方差为正时，说明X和Y是正相关关系；协方差为负时，说明X和Y是负相关关系；协方差为0时，说明X和Y是相互独立。Cov(X,X)就是X的方差。当样本是n维数据时，它们的协方差实际上是协方差矩阵(对称方阵)。例如，对于3维数据(x,y,z)，计算它的协方差就是：
$$
\operatorname{Cov}(X, Y, Z)=\left[\begin{array}{ccc}{\operatorname{Cov}(x, x)} & {\operatorname{Cov}(x, y)} & {\operatorname{Cov}(x, z)} \\ {\operatorname{Cov}(y, x)} & {\operatorname{Cov}(y, y)} & {\operatorname{Cov}(y, z)} \\ {\operatorname{Cov}(z, x)} & {\operatorname{Cov}(z, y)} & {\operatorname{Cov}(z, z)}\end{array}\right]
$$
### 4. Scatter Matrix : 

covariance matrix的估计。(有时候covariance matrix  很难去计算)， 同样应用在high dimension reduction. 
![markdown](/assets/images/scatter_matrix.png)

