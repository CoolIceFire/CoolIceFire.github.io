---
layout:     post
title:      "Matrix Factorization"
subtitle:   "Matrix Factorization For Recommender Systems"
date:       2016-05-30
author:     "LiGuang"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 推荐系统
---

> “Yeah It's on. ”


## 前言

近年来，隐语义模型(Latent Factor Model)受到人们的热捧，与之相关的模型有矩阵分解(Matrix Factorization)、LDA(Latent Dirichlet Allocation)等。在Netflix Prize比赛中，Yehuda Koren凭借矩阵分解模型获得冠军，这篇主要是分享一下Yehuda Koren的Matrix Factorization Techniques For Recommender Systems这篇论文，分享一下自己的理解，并利用MovieLens数据进行实验。


<p id = "build"></p>
---

## 正文

从字面上来看，矩阵分解就是把矩阵分解成多个矩阵然后进行组合运算，在推荐系统中常用的是乘法运算。在实际应用中，User-Item矩阵是非常大的，比如淘宝网等都有上亿的User和Item,这样就出现了一个问题，在User-Item矩阵中，绝大多数的值都是空值，User-Item矩阵是一个非常非常稀疏的矩阵。然而在著名的矩阵分解算法—SVD中，User-Item矩阵中不能够存在空值。如果使用SVD，那么则需要将空值补为0或者随机值，很显然对用户来说是不合理的。

矩阵分解的思想是用户的兴趣受少数几个因素的影响，因此可以稀疏高维的User-Item矩阵转化为两个低维矩阵相乘，然后利用随机梯度下降的方法求最优解。

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160530/mf-01.png)

上图中，左边的矩阵为User-Item矩阵R，中间为Item特征矩阵Q，右边为User特征矩阵P。实际上就是讲User-Item矩阵R分解，即R=Q·P。好了，现在问题转化为找到两个矩阵使其相乘之后的值，和User-Item矩阵中有值的位置中的值尽可能接近，此时，空值也已经计算出，并且比较接近实际值。

用梯度下降的方法很容易求解到矩阵P与矩阵Q。在Yehuda Koren的paper中，cost function为：
![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160530/mf-02.gif)

第一项是误差项，第二项是正则化项，用来防止过拟合，最小化cost function即可求得P和Q。

参数更新可以利用paper中的公式：
![](http://www.forkosh.com/mathtex.cgi?e_{ui}= r_{ui}-q_{i}^{T}p_{u})

![](http://www.forkosh.com/mathtex.cgi?q_{i}\leftarrow q_{i}+\gamma \left ( e_{ui}p_{u}-\lambda q_{i} \right ))

![](http://www.forkosh.com/mathtex.cgi?p_{u}\leftarrow p_{u}+\gamma (e_{ui}q_{i}-\lambda p_{u}))

多次迭代可以求得最优解。
以上是最简单的形式，还可以添加各种约束进行改进，例如：
![](http://www.forkosh.com/mathtex.cgi?r_{ui} = \mu +b_{i}+b_{u}+q_{i}^{T}p_{u})

等式右边参数从左到右分别为：

* 所有item评分，对于固定的数据集是一个常数，可直接算出。

* 用户u的偏置：表示的是特定用户的打分习惯，比如有的人倾向于打高分，有的人则倾向于打低分，这个值可以通过训练得到。

* 物品i的偏置：表示某一特定物品得到的打分情况，独立于用户兴趣因素。例如：经典好片评分普遍很高，差的片则分低。

另外，paper中还介绍了时间因素对结果的影响，这是由于人的兴趣会随着时间发生变化。

矩阵分解的优缺点：

* 训练精度要比较高，高于UserCF和ItemCF等。
* 编程实现较为容易，用随机梯度下降方法即可训练得到模型。
* 扩展性比较好，可以添加隐形反馈，时间动态信息等。
* 训练费时。
* 不容易从现实生活中进行解释，User特征矩阵和Item特征矩阵的每个维度无法用现实概念进行解释。
 
## 实验
看完paper后，用MovieLens的数据进行了实验，用的100K中的u1.base和u1.test，train set的rmse是1.0，test set的rmse1.1左右，好像是有点高，再慢慢调调参数试试。

代码：请戳[这里。](https://github.com/CoolIceFire/RS/tree/master/MatrixFactorization)

## 后记

第一次用markdown写，格式啥的不熟悉……可能有点乱。用LaTex写的公式应该没错……

错误不足之处欢迎指正。

--写于2016-05-30


