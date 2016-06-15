---
layout:     post
title:      "Deep content-based music recommendation"
subtitle:   "Recommending music on Spotify with deep learning"
date:       2016-06-15
author:     "LiGuang"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 推荐系统
---

> “Yeah It's on. ”


## 前言

利用CNN判断音乐之间的相似度
<p id = "build"></p>
---

## 简介
文中提出了一种解决音乐推荐冷启动问题的方法，通过用户收听的音乐的音频信息构建一个潜在因子模型进行推荐，并且对传统音频词袋模型与将深度卷积网络应用于音频的两种方法进行了对比。
## 正文
一种传统的协同过滤的方法是通过用户所消费或者评分的项目组合来推测出用户的喜好信息以及项目的彼此相关性；另一种方法是利用物品内容和元数据进行预测用户的喜好。通常情况下，这两种方法是胜过基于内容推荐的，但是它需要用户数据。

基于内容的推荐的一个难点是需要一个能够提取音乐高层次属性(流派、情绪、乐器和抒情主题等)的复杂结构。以往的方法是依靠音频特性(梅尔倒频谱系数)作为输入，然后用SVM/LR等进行分类。paper作者用深度卷积网络来训练音乐音频来预测潜在因子。评价音频信号对用户偏好的影响程度以及模型在音乐推荐中的效果。

本文中利用音乐音频对潜在因子预测使用了两种方法，一种是从音频信号中提取局部特征，并将其放入词带，使用词带模型，然后用传统的回归方法实现特征到潜在因子的映射；另一种是使用深度卷积网络。

## 架构
下面是架构图：
![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160614/01.png)
从图中可以看出，它包括four convolutional layers && three dense layers.这个架构与图像处理中的CNN架构还稍有不同。

**输入**：具有599帧和128个频点的一系列梅尔频谱。梅尔频谱是一种时频特性，从音频信号的窄窗口经过短时傅里叶变换得到。每一个变换得到一帧，然后将连续的帧排列成一个矩阵，形成输入的声谱。最后降低维数变成梅尔刻度。

**卷积层**：使用了ReLu，激活函数是max(0,x)，卷积都是在一维上进行的。

**池化**：降低时域中间表征采样率，增大过程的时不变性。第一层与第二层中池化的尺寸为4，后面的是2.

**全局池化层**：覆盖整个时间轴，计算时域学习特征的统计值。采用三种池化：mean，maximum,L2-norm.

后续讲了些关于训练的一些细节性的知识，感觉需要较多的声学知识才能理解，后面理解的不是很明白，就不说了……
## 后记

这篇文章对于自己来说有点难，需要深度学习、信号与系统、声学的知识综合。就写一些自己觉得明白的地方吧。下面参考1中原文非常详细，想要了解的**推荐读参考一**。

不过，后面看了一篇Google的论文，里面说用mfcc的效果并不是很好……它们又用别的声学特性进行了实验，可以两篇综合起来看看。还有人用RNN进行CF，看他文中写的貌似结果也不错，改天研究研究。。。

不正之处，欢迎指出！

## 参考
1. [Recommending music on Spotify with deep learning](http://benanne.github.io/2014/08/05/spotify-cnns.html)
2. [Deep content-based music recommendation](http://papers.nips.cc/paper/5004-deep-content-based-music-recommendation.pdf)
3. [TEMPORAL POOLING AND MULTISCALE LEARNING FOR AUTOMATIC ANNOTATION AND RANKING OF MUSIC AUDIO](http://ismir2011.ismir.net/papers/PS6-13.pdf)
