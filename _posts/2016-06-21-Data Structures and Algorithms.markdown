---
layout:     post
title:      "Data Structures and Algorithms"
subtitle:   "数据结构与算法"
date:       2016-06-16
author:     "LiGuang"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 算法
---

> “Yeah It's on. ”


## 前言
快找工作了，再写写常见的数据结构与算法，熟悉一下，本文持续不定期更新，内容会包括排序算法、树、图算法、字符串算法等......不正之处，欢迎指出。
<p id = "build"></p>
---

## 排序

**冒泡排序**

基本思想：两两比较相邻的元素的关键字，如果反序则交换。

过程：

1.比较相邻的元素。如果第一个比第二个大，就交换他们两个。

2.对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大
的数。

3.针对所有的元素重复以上的步骤，除了最后一个。

4.持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

复杂度：时间复杂度是O(n^2)，空间复杂度是O(1)。

参考代码：

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160616/01.png)

**选择排序**

原理：每次在未排序的元素中找出最小(大)的元素，将其放到已排序的序列末尾。

复杂度：时间复杂度O(n^2)，空间复杂度O(1)。

参考代码：

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160616/02.png)

**插入排序**

原理：对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。

复杂度：时间复杂度O(n^2)，空间复杂度O(1)。

参考代码：

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160616/03.png)

**快速排序**

原理：使用分治法把一个序列分为两个子序列排序，然后合并。

步骤：

1. 从数列中挑出一个元素，称为"基准"（pivot），

2. 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区结束之后，该基准就处于数列的中间位置。这个称为分区（partition）操作。

3. 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序。

复杂度： 时间复杂度O(nlogn)

参考代码：
![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160617/01.png)

**归并排序**

归并算法指的是将两个已排序的序列合并成一个序列的操作，其递归法原理如下：

1. 将序列每相邻两个数字进行归并排序，形成floor(n/2)个序列，排序后每个序列包含两个元素。
2. 将上述序列再归并，形成floor(n/4)个序列，每个序列包含四个元素。
3. 重复步骤2，直到所有的元素排序完毕。

复杂度：时间复杂度为O(nlogn)

参考代码：
![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160621/011.png)
![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160621/012.png)

**堆排序**

堆排序指的是利用堆这种数据结构设计的一种排序算法，结构近似于完全二叉树，其子节点的键值或索引总是小于(或者大于)它的父节点。其方法如下：

1. 调整待排序数组，使其成为最大(小)堆。
2. 堆化数组之后，第一次将arr[0]与arr[n-1]交换(因为arr[0]是当前的最大(小)值)，然后堆化数组arr[0,...,n-2]
3. 将arr[0]与arr[n-2]交换，与步骤2相同。
4. 直到arr[0]与arr[1]交换，排序结束。

复杂度：时间复杂度O(nlogn)。

参考代码：
![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160621/013.png)

**计数排序**

计数排序是使用一个额外的数组C，其中第i个元素是待排序数组A中值等于i的元素的个数，然后根据数组C来将A中的元素排到正确的位置。

复杂度：时间复杂度O(n+k)，空间复杂度O(k)，k为待排序数组中最大的数与最小数之差+1.

参考代码：
![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160621/021.png)

## 树
**线段树**
线段树是一种二叉搜索树，将一个区间划分为多个单元区间，每个单元区间对应线段树上的一个节点。对于一个节点区间[a,b]，左子树表示的区间是[a, (a+b)/2]，右子树表示的区间是[(a+b)/2+1, b]，因此线段树是平衡二叉树。

从一个例子讲一下线段树，问题描述为：从数组arr[0,...,n-1]中查找某个区间内的最小值，其中数组大小固定，但是数组中的元素的值可以随时更新。这个问题最耿直的做法是：遍历该区间找到最小值，时间复杂度是O(n)，但是当查询非常频繁的时候，时间复杂度是O(mn)，可能会不满足要求。而使用线段树则是一个很好的解决方法，如下图所示：

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160622/01.jpg)

线段树预处理耗时O(n)，查询、更新操作耗时O(logn)。下面就以上图写一下线段树的程序，数组大小为6，初始元素值为2，5，1，4，9，3，然后我们定义两种输入，第一种： 1 L R，表示的是查询[L,R]内的最大值；第二种：2 p val，表示的是对数组中第p个元素的值加上val，即arr[p-1]+val。PS：写完程序弄完图片才发现上图是查询最小值……把程序中max换成min就是最小值了。。。我这就不换了，程序中也写了注释，对比着运行结果带进去试试就很明白了。(代码就是图片形式的)

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160622/02.png)

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160622/03.png)

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160622/04.png)

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160622/05.png)

**AVL**

AVL树使一种自平衡二叉查找树，在AVL树中任何两个子树的高度最大差别为1，它因此也被成为高度平衡树。查找、插入和删除在平均和最坏情况下都是O(logn)，增加和删除可能通过一次或多次树旋转来重新平衡这个树，节点的平衡因子是它的左子树的高度减去它的右子树的高度，带有平衡因为1、0或-1的节点被认为是平衡的，带有平衡因子-2或2的节点被认为是不平衡的，并需要重新平衡这课树。平衡因子可以直接存储在每个节点中，也可以从节点的子树高度计算出来。

AVL树中主要是处理四种旋转问题，如下图所示(来自维基百科)：

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160718/01.png)

根据上图很容易能够写出各种旋转了，下面是简易实现：

	#include <cstdio>
	#include <iostream>
	#include <string>
	#include <cstdlib>
	#include <vector>
	#include <queue>
	
	using namespace std;
	
	typedef struct AvlNode
	{
	    double value;
	    AvlNode* left;
	    AvlNode* right;
	    int height;
	}AvlNode;
	
	int GetHeight(AvlNode*);
	AvlNode* InsertTree(double, AvlNode*);
	AvlNode* LLRotate(AvlNode*);
	AvlNode* LRRotate(AvlNode*);
	AvlNode* RRRotate(AvlNode*);
	AvlNode* RLRotate(AvlNode*);
	
	int GetHeight(AvlNode* node)
	{
	    return node ? node->height : -1;
	}
	
	AvlNode* InsertTree(double value, AvlNode* t)
	{
	    if (t == NULL)
	    {
	        t = (AvlNode*)malloc(sizeof(AvlNode));
	        t->value = value, t->height = 0;
	        t->left = t->right = NULL;
	    }
	    else
	    {
	        if (value < t->value)
	        {
	            t->left = InsertTree(value, t->left);
	            if (GetHeight(t->left)-GetHeight(t->right) == 2)
	            {
	                if (value < t->left->value) t = LLRotate(t);
	                else t = LRRotate(t);
	            }
	        }
	        else if (value > t->value)
	        {
	            t->right = InsertTree(value, t->right);
	            if (GetHeight(t->right)-GetHeight(t->left) == 2)
	            {
	                if (value < t->right->value) t = RLRotate(t);
	                else t = RRRotate(t);
	            }
	        }
	    }
	    t->height = max(GetHeight(t->left), GetHeight(t->right)) + 1;
	    return t;
	}
	
	//left-left rotate
	AvlNode* LLRotate(AvlNode* t)
	{
	    AvlNode* tmp = t->left;
	    t->left = tmp->right;
	    tmp->right = t;
	    t->height = max(GetHeight(t->left), GetHeight(t->right))+1;
	    tmp->height = max(GetHeight(tmp->left), GetHeight(tmp->right))+1;
	    return tmp;
	}
	
	//right-right rotate
	AvlNode* RRRotate(AvlNode* t)
	{
	    AvlNode* tmp = t->right;
	    t->right = tmp->left;
	    tmp->left = t;
	    t->height = max(GetHeight(t->left), GetHeight(t->right))+1;
	    tmp->height = max(GetHeight(tmp->left), GetHeight(tmp->right))+1;
	    return tmp;
	}
	
	//left-right rotate
	AvlNode* LRRotate(AvlNode* t)
	{
	    t->left = RRRotate(t->left);
	    return LLRotate(t);
	}
	
	//right-left rotate
	AvlNode* RLRotate(AvlNode* t)
	{
	    t->right = LLRotate(t->right);
	    return RRRotate(t);
	}
	
	//find the target in avl-tree
	bool Find(AvlNode* t, double target)
	{
	    if (t == NULL) return false;
	    if (t->value == target) return true;
	    if (t->value > target) return Find(t->left, target);
	    return Find(t->right, target);
	}
	
	//ouput the avl-tree
	void PrintTree(AvlNode* t)
	{
	    queue<AvlNode*> qu;
	    qu.push(t);
	    while (!qu.empty())
	    {
	        AvlNode* tmp = qu.front();
	        qu.pop();
	        if (tmp == NULL) cout << " * ";
	        else
	        {
	            cout << " " << tmp->value << " ";
	            qu.push(tmp->left);
	            qu.push(tmp->right);
	        }
	    }
	    cout << endl;
	}
	
	int main()
	{
	    AvlNode *t = NULL;
	    while (true)
	    {
	        double a, b;
	        cin >> a >> b;
	        if (a == 1) t = InsertTree(b, t);
	        else cout << "find:" << Find(t, b) << endl;
	        PrintTree(t);
	    }
	}

删除写起来稍微麻烦一点，涉及到各种调整，在这没写……简单的方法可以利用懒惰删除，设置一个标记位来表示该节点是否被删除。


**红黑树**

红黑树是AVL树的一种变种，红黑树的操作在最坏情形下花费O(logN)时间，与AVL树相比插入操作可以相对更容易的完成。

红黑树是具有下列着色性质的二叉查找树：

1. 每一个节点或者着成红色，或者着成黑色。
2. 根是黑色的。
3. 如果一个节点是红色的，那么它的子节点必须是黑色的。
4. 从一个节点到一个NULL指针的每一条路径必须包含相同数目的黑色节点。
5. 所有的叶子都是黑色的(叶子是NIL节点)。

红黑树的高度最多是2log(N+1)，它可以在O(logn)时间内查找，插入和删除。

红黑树的代码以及讲解在wiki上讲解的非常详细，非常棒，墙裂推荐看[**wiki**](https://zh.wikipedia.org/wiki/%E7%BA%A2%E9%BB%91%E6%A0%91)

持续更新ing......
