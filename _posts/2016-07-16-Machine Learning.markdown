---
layout:     post
title:      "Machine Learning and Kaggle"
subtitle:   "机器学习与Kaggle"
date:       2016-07-16
author:     "LiGuang"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 机器学习
---

> “Yeah It's on. ”


# 前言
本文通过解决kaggle上的部分题目，介绍机器学习中的一部分算法，错误或不足之处欢迎指正！

<p id = "build"></p>
---

# 简介

# kNN
本段是利用k-近邻(kNN)算法解决kaggle上的[Digit Recognizer](https://www.kaggle.com/c/digit-recognizer)题目。训练集/测试集数据可以[下载](https://www.kaggle.com/c/digit-recognizer/data)。

k-近邻(kNN)算法的工作原理是：存在一个样本数据集合，集合中每个数据都存在标签，即我们知道样本集合中每组数据与其所属分类的对应关系。输入没有标签的数据后，将新数据与样本数据中的对应特征进行比较，然后提取出样本集中最相似数据的标签。通常，我们选取样本数据集中前k个最相似的数据，这就是k-近邻算法中的k，最后选取这k个最相似数据的标签的众数作为新数据的分类。

训练集：每一组数据包括一个标签以及784个像素值，在这里把像素值01化。

测试集：每一组数据包括784个像素值，同样把像素值01化。

以下是实现[代码](https://github.com/CoolIceFire/ML/blob/master/ML_By_Kaggle/kNN_Digit_Recognizer.py)：

	from numpy import *
	import operator
	import csv
	
	#flag表示是否为label，如果非label，则把所有>0值处理为1，其余为0
	def Str2Int(data, flag):
		data = matrix(data)
		new_data = zeros(data.shape)
		for i in range(data.shape[0]):
			print(i)
			for j in range(data.shape[1]):
				if flag == True:
					new_data[i, j] = 1 if int(data[i, j]) > 0 else 0
				else:
					new_data[i, j] = int(data[i, j])
		return new_data
	
	#获取训练集数据
	def GetTrainData(file_path):
		l = []
		line_cnt = 0
		with open(file_path) as file:
			lines = csv.reader(file)
			for line in lines:
				if line_cnt != 0:
					l.append(line)
				line_cnt += 1
		l = matrix(l)
		labels = l[:, 0]
		data_set = l[:, 1:]
		print('a')
		return Str2Int(data_set, True), Str2Int(labels, False)
	
	#获取测试集数据
	def GetTestData(file_path):
		data = []
		line_cnt = 0
		with open(file_path) as file:
			lines = csv.reader(file)
			for line in lines:
				if line_cnt != 0:
					data.append(line)
				line_cnt += 1
		return Str2Int(data, True)
	
	#kNN
	def ClassifyKnn(test_data, train_data, train_labels, k):
		train_data_size = train_data.shape[0]
		diff_mat = tile(test_data, (train_data_size, 1)) - train_data
		square_diff_matt = diff_mat**2
		square_distances = square_diff_matt.sum(axis = 1)
		distances = square_distances**0.5
		sorted_dis_index = distances.argsort()
		class_count = dict()
		for i in range(k):
			label = train_labels[sorted_dis_index[i], 0]
			class_count[label] = class_count.get(label, 0) + 1
		sorted_class_count = sorted(class_count.iteritems(), key=operator.itemgetter(1), reverse=True)
		return sorted_class_count[0][0]
	
	if __name__ == '__main__':
		train_data_set, train_labels = GetTrainData('E:\\DL\\DR\\train.csv') 
		test_data_set = GetTestData('E:\\DL\\DR\\test.csv')
		cnt = 1
		for test_data in test_data_set:
			print(cnt, ClassifyKnn(test_data, train_data_set, train_labels, 20))
			cnt += 1

优点：简单、易于理解、容易实现、无需估计参数、不需要训练、适合多分类问题、可以并行计算

缺点：懒惰算法、计算量大、内存开销大、可解释性差

主要考虑问题：K的取值，训练样本是否一视同仁，能否在减少训练样本数的同时保持精度等

# ID3
ID3算法是一个分类预测算法，该算法的核心是“信息熵”问题，熵定义为信息的期望值，熵的计算公式为

![](https://raw.githubusercontent.com/CoolIceFire/CoolIceFire.github.io/master/img/20160716/01.gif)

得到熵之后，按照获取最大信息增益的方法划分数据集。信息增益在此处的计算方法为数据集划分前后熵的变化。ID3存在很大的局限性，那就是ID3算法无法直接处理数值型数据，尽管可以通过量化的方法将数值型数据转化为标称型数值，但是特征太多时，仍然存在很多问题。在Kaggle上没有能直接用ID3算法进行解决的题目，所以在此没有数据集，我们可以假设数据的输入格式为 [特征(1,2...n),标签]。另外，实现中没有进行剪枝。

数据集是关于车辆选择的，来自[UCI Car Evaluation](http://archive.ics.uci.edu/ml/datasets/Car+Evaluation)。

训练集：[这里](https://github.com/CoolIceFire/ML/edit/master/ID3/train.csv)

测试集：[这里](https://github.com/CoolIceFire/ML/edit/master/ID3/test.csv)，从训练集中随意挑选的。

代码：[这里](https://github.com/CoolIceFire/ML/blob/master/ID3/ID3.py)

	# -*- coding:utf-8 -*- 
	from numpy import *
	import operator
	from math import *
	import csv
	import os
	
	# 树节点结构
	class TreeNode:
		def __init__(self, features=None, node=None, stop_flag=False, tag=None, feature_index=-1):
			self.features = [] #该节点的特征集合
			self.node = [] #该节点的子节点集合
			self.stop_flag = stop_flag #是否停止分裂
			self.tag = tag #分类
			self.feature_index = feature_index #该特征的下标
	
	# 分割数据集，把data[index]==value的挑选出来
	def SplitDataSet(data_set, index, value):
		ret_data = []
		for data in data_set:
			if data[index] == value:
				tmp_data = data[ : index]
				tmp_data.extend(data[index+1 : ])
				ret_data.append(tmp_data)
		return ret_data
	
	# 计算熵
	def CalcEntropy(data_set):
		labels_set = set([data[-1] for data in data_set])	
		labels_dict = dict()
		for data in data_set:
			labels_dict[data[-1]] = labels_dict.get(data[-1], 0) + 1
		entropy = 0.0
		for key in labels_dict:
			prob = 1.0*labels_dict[key]/len(data_set)
			entropy -= 1.0*prob*log(prob, 2)
		return entropy
	
	# 挑选出最优的特征
	def SelectBestFeature(data_set):
		num_features = len(data_set[0])-1
		pre_entropy = CalcEntropy(data_set)
		max_entropy_gain = 0.0
		best_feature_index = -1
		for i in range(num_features):
			features_set = set([data[i] for data in data_set])
			tmp_entropy = 0
			for feature in features_set:
				tmp_data = SplitDataSet(data_set, i, feature)
				tmp_entropy += 1.0*len(tmp_data)/len(data_set)*CalcEntropy(tmp_data)
			if (pre_entropy-tmp_entropy) > max_entropy_gain:
				max_entropy_gain = pre_entropy-tmp_entropy
				best_feature_index = i
		return best_feature_index
	
	# 建树
	def BuildTree(data_set):
		if len(data_set[0]) == 2: #如果数据只有一个特征+一个标签，那么就应该停止分裂
			labels_dict = dict()
			for data in data_set:
				labels_dict[data[-1]] = labels_dict.get(data[-1], 0) + 1
			sorted_labels_dict = sorted(labels_dict.iteritems(), key=operator.itemgetter(1), reverse=True)
			return TreeNode( None, None, True, sorted_labels_dict[0][0], -1)
		best_feature_index = SelectBestFeature(data_set)
		features_set = set()
		labels_set = set()
		for data in data_set:
			features_set.add(data[best_feature_index])
			labels_set.add(data[-1])
		tree_node = TreeNode( None, None, False, None, best_feature_index)
		if len(labels_set) == 1:
			tree_node.stop_flag = True
			tree_node.tag = data_set[0][-1]
			return tree_node
	
		for feature in features_set:
			tree_node.features.append(feature)
			tree_node.node.append(BuildTree(SplitDataSet(data_set, best_feature_index, feature)))
		return tree_node
	
	#获取训练数据
	def GetTrainData(file_path):
		data = []
		with open(file_path) as file:
			lines = csv.reader(file)
			for line in lines:
				data.append(line)
		return data
	
	#获取测试数据
	def GetTestData(file_path):
		data = []
		with open(file_path) as file:
			lines = csv.reader(file)
			for line in lines:
				data.append(line)
		return data
	
	# 预测
	def Predict(decision_tree, test_data):
		if decision_tree.stop_flag == True:
			return decision_tree.tag
		feature_index = decision_tree.feature_index 
		for i in range(len(decision_tree.features)):
			if decision_tree.features[i] == test_data[feature_index]:
				test_next_data = test_data[:feature_index]
				test_next_data.extend(test_data[feature_index+1:])
				return Predict(decision_tree.node[i], test_next_data)
	
	if __name__ == '__main__':
		train_data_set = GetTrainData('C:\\Users\\lg\\Desktop\\id31.csv')
		test_data_set = GetTestData('C:\\Users\\lg\\Desktop\\test.csv')
		decision_tree = BuildTree(train_data_set)
		for test_data in test_data_set:
			print(test_data[-1], Predict(decision_tree, test_data))

# Logistic回归
Logistic Regression可以简单的描述为下面几个过程：

1. 寻找合适的预测函数(hypothesis),常常用h函数来表示，它用来预测输入数据的输出。
2. 构造损失函数(cost function)，该函数可以用训练集数据的输出与预测输出之差来表示(y-h)，所用训练数据的损失记为J(θ) 
3. 求参数使损失函数的值最小。

有多种算法可以用来进行参数求解，比如梯度下降、随机梯度下降、牛顿法、拟牛顿法、BFGS、L-BFGS等等，可以阅读相关的资料推导等。下面是用随机梯度算法的简单实现。解决kaggle上的[Digit Recognizer](https://www.kaggle.com/c/digit-recognizer)题目。训练集/测试集数据可以[下载](https://www.kaggle.com/c/digit-recognizer/data)。

	# -*- coding:utf-8 -*-
	from numpy import *
	import operator
	import csv
	
	#flag表示是否为label，如果非label，则把所有>0值处理为1，其余为0
	def Str2Int(data, flag):
		print('Str2Int')
		data = matrix(data)
		new_data = zeros(data.shape)
		for i in range(data.shape[0]):
			for j in range(data.shape[1]):
				if flag == True:
					new_data[i, j] = 1 if int(data[i, j]) > 0 else 0
				else:
					new_data[i, j] = int(data[i, j])
		return new_data
	
	#获取训练集数据
	def GetTrainData(file_path):
		print('GetTrainData')
		l = []
		line_cnt = 0
		with open(file_path) as file:
			lines = csv.reader(file)
			for line in lines:
				if line_cnt != 0:
					l.append(line)
				line_cnt += 1
		l = matrix(l)
		labels = l[:, 0]
		data_set = l[:, 1:]
		return Str2Int(data_set, True), Str2Int(labels, False)
	
	#获取测试集数据
	def GetTestData(file_path):
		print('GetTestData')
		data = []
		line_cnt = 0
		with open(file_path) as file:
			lines = csv.reader(file)
			for line in lines:
				if line_cnt != 0:
					data.append(line)
				line_cnt += 1
		return Str2Int(data, True)
	
	def sigmoid(x):
		return 1.0/(1 + exp(-x))
	
	def GetError(weights, data_set, labels):
		error = 0
		for i in range(shape(data_set)[0]):
			h = sigmoid(sum(data_set[i]*weights))
			h = 1 if h > 0.5 else 0
			error += abs(labels[i]-h)
		return error*1.0/shape(data_set)[0]
	
	def SGD(now, data_set, labels, num_iters, episilon = 0.0001):
		m, n = shape(data_set)
		print(m, n)
		weights = zeros(n)
		pre = 100
		alpha = 0.0001
		for iter in range(num_iters):
			data_index = range(m)
			if iter%50 == 0: alpha *= 0.9
			for i in range(m):
				index = int(random.uniform(0, len(data_index)))
				h = sigmoid(sum(data_set[index]*weights))
				error = labels[index] - h
				weights = weights + alpha * error * data_set[index]
				del(data_index[index])
			error_rate = GetError(weights, data_set, labels)
			if error_rate < episilon:
				break
			print('Now: %d, Iter: %d, %f' % (now, iter, error_rate))
		return weights
	
	def GetPredict(test_data, weights, pro, now):
		m, n = shape(test_data)
		for i in range(m):
			pro[i, now] = sigmoid(sum(test_data[i]*weights))
	
	if __name__ == '__main__':
		train_data_set, train_labels = GetTrainData('E:\\DL\\DR\\train.csv')
		test_data_set = GetTestData('E:\\DL\\DR\\test.csv')
		pro = zeros((shape(test_data_set)[0], 10))
		for i in range(10):
			print('begin: ', i)
			labels = [1 if train_labels[j, 0] == i else 0 for j in range(shape(train_labels)[0])]
			weights = SGD(i, train_data_set, labels, 1000)
			GetPredict(test_data_set, weights, pro, i)
		m = shape(test_data_set)[0]
		for i in range(m):
			mn = -1; idx = -1
			for j in range(10):
				if pro[i, j] > mn:
					mn = pro[i, j]
					idx = j
			print(i+1,idx)

参数的问题，准确率并不高，90%多点，可以调一下参数测试下参数不同时的效果。

优点：计算代价不高，易于实现和理解。

缺点：容易欠拟合，分类精度可能不高。


# 后记

# 参考
