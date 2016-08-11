---
layout:     post
title:      "Sampling Method"
subtitle:   "抽样方法"
date:       2016-07-28
author:     "LiGuang"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 机器学习
---

> “Yeah It's on. ”


## 前言


<p id = "build"></p>
---

## 简介
学习一下各种抽样方法……

## 蓄水池抽样
下面是一个问题：在不知道文件总行数的情况下，如何从文件中随机的抽取一行？

在知道文件总行数的情况下，这个问题是很容易解决的，生成一个随机数就可以了，但是现在并不知道文件的行数，那该怎么办呢？利用蓄水池抽样。方案是这样的，设取出的行号为x，第一次直接以第一行作为x，而后第二次以1/2的概率决定是否用第二行替换x，第三次以1/3的概率决定是否替换x，以此类推。
证明也很简单，第一次时:P(x=1)=1/1;第二次P(x=1)=1/1*1/2=1/2，P(x=2)=1/2;第三次P(x=1)=1/2*(1-1/3)=1/3，P(x=2)=1/3,P(x=3)=1/3,以此类推……

问题可扩展为：如何从未知样本中抽取k个数？
方法和前面一样的，先把前k个数中放入蓄水池，对于第k+1，以k/(K+1)的概率决定是否将其放入蓄水池中，若放入，则随机从蓄水池中抽取一个替换，所以概率为K/(k+1)*1/k=1/(k+1)， 即等概率抽取。
	
简单代码：

	for i = k+1:N
		j = random(1,i)
		if j < k
			swap(ith, jth)
		

##蒙特卡洛数值积分

![](https://github.com/CoolIceFire/CoolIceFire.github.io/blob/master/img/20160810/1.jpg?raw=true)

简单示例：求f(x)=e^(2x+1)在[3,5]上的积分

根据图中的推导可得：a=3、b=5、由于f(x)在[3,5]上是单调递增函数，所以c=f(3)=exp(7),d=f(5)=exp(11)，简单代码如下，假设n=10^5,结果为29230.920253，直接计算积分约为29388.7542784

	from numpy.random import *
	from numpy import *
	
	n = 10000
	a = 3; b = 5; u = 0
	c = exp(7); d = exp(11)
	def g(x):
		return exp(2*x+1)
	
	def f(y):
		return (g(a+(b-a)*y)-c)/(d-c)
	
	for i in range(n):
		x = ranf()
		y = ranf()
		if y <= f(x): u += 1
	print((b-a)*(d-c)*(1.0*u/n)+c*(b-a))
	print((exp(11)-exp(7))*0.5)


当然还可以用求均值的方法，即J=sum(f(xi))/n.

## Box-Muller变换

基本思想是先得到服从均匀分布的随机数然后将其转变为服从正态分布的随机数。推导如下：

![](https://github.com/CoolIceFire/CoolIceFire.github.io/blob/master/img/20160810/2.jpg?raw=true)

## 接受-拒绝抽样(Acceptance-Rejection Sampling)
![](https://github.com/CoolIceFire/CoolIceFire.github.io/blob/master/img/20160810/3.png?raw=true)

在实际的应用场景中，有时我们很难直接采样f(x)，我们可以用一个容易采样的方法g(x)进行采样，然后其中的一部分样本作为有效的样本，另一部分则认为是无效的舍弃掉。但是它需要满足几个条件：

	1. 对于任意一个x，有f(x) <= M*g(x)
	2. g(x)容易采样
	3. g(x)的形状最好比较接近f(x)

采样过程如下：
	
	1. 利用g(x)获得一个采样样本xi
	2. 均匀分布采样 ui~U(0,1)
	3. 如果f(xi)/(M*g(xi)) >= ui，则接受这次采样；否则拒绝
	4. 重复上述步骤，知道获得足够的样本

## 后记

## 参考
不断更新……
