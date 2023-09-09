 LEARNING STRUCTURED REPRESENTATIONS BY EMBEDDING CLASS HIERARCHY
日期：2023-09-09  时间：16:44

# keywords

hierarchical label, tree structure, cophenetic correlation coefficient([[CPCC]]) score, 

# Background

1. 现有算法没有将结构化表示学习作为训练目标，也没有嵌入树结构
2. 分层标签的使用

# Method

## 改进了CCPC的距离度量

传统CCPC：*一个距离是由两个根节点之间的距离表示*，另一个是两个点之间的欧式距离

改进结果：*第一个距离改为两个点之间从树状结构之间最近连线的距离与根节点层级差距之差*，另一个仍是两个点之间的欧氏距离

核心：将节点的距离从树状结构中走，得到了分层的一些信息。

算法复杂度变化较小

## 嵌入到损失函数中

loss = CEloss（分类结果， 标签）- lambda CCPC（树距离， 欧式距离）
# Experiment

## dataset

MNIST

CIFAR100

BREEDS

## experimental setting

exp1：损失函数是否增加CCPC

exp2：zero-shot， 损失函数是否增加CCPC

## comparison framework

算法本身

不加CCPC

将分层学习改为多任务学习

先用粗标签学习再微调结果

## effect

exp1：增加CCPC效果只会增加

exp2：在最高分类级别上效果变差，但在粗类别上效果变好

# Future

## problem

在CIFAR中增加粗类别的选取是随机的，MNIST也是，没有与实际的特征之间有联系

在exp2的细类别效果反而下降

## improvement

优化树状结构，将树状结构之间的距离根据实际修改

实验时的分层标签不能硬凑，得从某些角度入手