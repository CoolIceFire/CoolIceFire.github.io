---
layout:     post
title:      "User Fatigue in Online News Recommendation"
subtitle:   "在线新闻推荐中用户疲劳问题"
date:       2016-06-15
author:     "LiGuang"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 推荐系统
---

> “Yeah It's on. ”


## 前言

这篇就随便看看吧，个人觉得质量不算好。。。
<p id = "build"></p>
---

## 简介
现如今，许多新闻推荐系统能够根据不同的用户进行个性化推荐，而且效果还很不错。不过，很多都忽略了一个问题，那就是用户疲劳问题。推荐系统总是向用户推荐大量相同主题的新闻，导致用户产生疲劳，从而导致点击量的下降。因此，在某些条件下，先前推荐的主题的等级应当降低，其他时间时应当适当提升。利用Bing Now进行实验。
## 正文
用户疲劳是在线推荐系统中普遍存在的问题，它表示的是在反复推荐同一主题的项目下，用户很快失去兴趣的一个影响因子。在Bing Now中，通常是由编辑发表一些新的新闻放入一个集合中，替换掉旧新闻，然后系统从中挑选15个新闻放入Bing Now。通过计算用户的两次连续访问之间的重叠的新闻项目平均数来量化用户以何种程度访问推荐中反复出现的项目。
![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160614/02.png)

从上图中可以看出，随着两次访问时间间隔的增加，重叠的新闻项目数目越来越少。然而在实际情况中，有的人却喜欢点击同一个项目。所以，主要的任务是在何种情况下，应当减少或者增加先前看过的主题的新闻。文中对同一项目疲劳、用户特征、同一类别疲劳、物品位置影响以及时间相关因素做了实验。

用户分为三类：重度用户、中度用户、轻度用户。分类方法是：统计用户集合中所有用户的点击量N，然后将用户按照点击量进行排序，前m个用户占了点击量的1/3，这m个用户为重度用户；后x个用户占了1/3，这x个用户称为轻度用户，剩下的用户称为中度用户。重度、中度、轻度用户所占的比例为3%、10%、87%。

下面图中的**CTR**是**Click Through Rate**.

**同一项目疲劳**

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160615/01.png)

总体趋势是非常直观的，反复推荐同一项目大大降低了点击率。
![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160615/02.png)

上图表示的是用户先前点击过该项目次数对点击率的影响。看(a)中，对于所有用户来说，如果先前看过该新闻，那么点击率是要高于先前未看过该新闻的点击率。然而，只有Light User与(a)相符。这现象说明轻度用户与重度、中度用户的点击模式不同，重度、中度用户更倾向于点击不同的新闻。

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160615/03.png)

上图表示的是先前点击该新闻次数与连续点击该新闻次数不同对后续点击率的影响。

**用户特征**

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160615/04.png)

上面是分析性别、年龄因素，结果从图中很容易看出。。。

**同一类别**

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160615/05.png)

上图是同一类别疲劳的实验图，效果从图中很容易看出。。。文中还提到了另外一个实验，那就是不同的新闻主题对同一类别疲劳因素的影响。比如，总是给你推荐体育类的新闻和总是给你推荐商业类的新闻等等。。。

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160615/06.png)

还是娱乐新闻更能吸引眼球。。。

**位置影响**

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160615/08.png)
上图是新闻的位置(从左到右)不同对点击率的影响，结果从图中很容易得出。在位置8到9处剧烈下降的原因是在Bing Now中前8个是直接可见的，而后面的则需要向右侧滑动才能看到。

**时序因素**
![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160615/07.png)

## STOP

文中主要做了这么几个实验，看了后面的内容，是一些实验数据还有些总结。总结写的也是对实验结果的一些描述啥的。感觉这个paper质量并不是很高……不过在实际中还是有应用价值的。。。

个人觉得做新闻推荐这个，不能总是给用户推荐用户喜欢的，这样的确会疲劳啊，可以在其中夹杂一小部分与用户喜欢话题相关的新闻(这个又涉及到不同主题之间相似度的计算)，然后根据点击率反馈效果决定是否继续推送该话题等。
## 后记

不正之处，欢迎指出！

## 参考
1. [User Fatigue in Online News Recommendation](http://www2016.net/proceedings/proceedings/p1363.pdf)
