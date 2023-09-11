 Many-Class Few-Shot Learning on Multi-Granularity Class Hierarchy
日期：2023-09-10  时间：22:55

# keywords

meta learning, hierarchical label, attention, memory mechanism, few- shot learning

# Background

1. 多类别few-shot学习效果不好
2. 分层分类

# Method

![[Pasted image 20230910230056.png]]

## 分层标签的使用

总输出=高级输出\*低级输出， 总体输出表示样本的类别

## 分类器设计

KNN设置为1个最邻近即判断结果

MLP用于粗类别分类， KNN用于细类别分类。

原因：few-shot学习中样本少，KNN对于训练样本要求较小，MLP对于样本数目要求较高

## 元学习设计

元学习过程中：
- 在粗分类过程中，同时对于MLP以及KNN进行训练
- 在细分类过程中，只对MLP进行训练

正常训练过程：
- 粗分类：只对于MLP进行训练，不使用KNN
- 细分类，同时对MLP以及KNN进行训练

## 注意力机制

将CNN提取的特征使用attention，输出为一系列概率，最大值即为模型判断类别

## 记忆单元设计

构建一个n\*m的矩阵，n表示需要分类的类别，与attention输出数目一致；m表示元学习不同的任务数。
一个C矩阵，用于存放错误分类的输出
一个U矩阵， 用于找到k个错误最严重的分类错误情况，从而利用C矩阵对应的数值对于记忆矩阵进行更新

元学习中，不更新矩阵参数

训练过程中
- 如果输入一个样本，通过KNN判断类别与实际标签一致，那么与该类别对应的那一行的矩阵元素都进行加权加法，进行更新，从而更新聚类中心；
- 如果识别错误，那么将这一分类结果存到C矩阵里面，U矩阵错分时乘以一个小于1的系数，正确分类时乘以一个大于1的系数，统计一个epoch中的最小k个U位置，并利用C里面的数据替换掉这几个位置的结果。

整个记忆矩阵的m（任务数）可以认为是prototype网络中的几个中心点。

测试过程中，当新输入一个样本时，如果与哪个未知的相似度最高，则判断为该类（通过KNN判断）

# Experiment

## dataset

mcfsOmniglot（根据Omniglot增加粗类别）

mcfsImageNet（从ImageNet中挑选一部分，组成新的数据集，减少了粗类别的数目）

## experimental setting

exp1：20%样本数目，测试准确率

exp2：n-way，k-shot标准实验，ImageNet 数据集

exp3：消融实验

exo4：n-way，k-shot标准实验，Omniglot 数据集

## comparison framework

ResNet18

prototype network（few-shot方法）

relation network

## effect

exp1：准确率大幅上升，参数量上涨不大

exp2：效果相对prototype小幅上涨， 2%左右

exp3：其他模块都有作用，attention模块增加后效果小幅下降1%左右

exp4：效果小幅提升，不足1%

# Future

## problem

attention的作用？

k个替换数目大于1？

