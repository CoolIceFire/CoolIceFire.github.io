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


## 前言
本文通过解决kaggle上的部分题目，介绍机器学习中的一部分算法，错误或不足之处欢迎指正！

<p id = "build"></p>
---

## 简介

## kNN
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



## 后记

## 参考
