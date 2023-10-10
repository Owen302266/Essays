 Scene-Aware Label Graph Learning for Multi-Label Image Classification
日期：2023-10-04  时间：19:48

# keywords

场景感知，图模型，多标签学习（不分层）
Scene-aware, graph learning, multi-label

# Background

多标签图像分类任务：
隐式方法：	
	用序列模型或图模型利用标签之间潜在的共现关系
	问题是整个数据集上可能会有错误的交互
显示方法：
	直接从数据中对于标签之间的共现概率矩阵进行建模
	很难为每个图像学习一个准确的共现概率矩阵

# Method

![[Scene-Aware Label Graph Learning for Multi-Label Image Classification.png]]

## 特征提取

通过现成的模型，例如resnet，vit等，提取图像的特征，得到特征图

## 场景注意力机制

多标签学习的多个标签，通过现成的语言模型将其嵌入，得到每个标签对应的embedding，与特征提取的特征图通过注意力机制进行处理。

整个处理计算步骤称为[[low-rank bilinear pooling model]]低秩双线性池模型

公式为：
$$
	\mathbf{x}_{i j}=\mathbf{P}^{T}\left(\tanh \left(\left(\mathbf{U}^{T} \mathbf{v}_{j}\right) \odot\left(\mathbf{V}^{T} \mathbf{t}_{i}\right)\right)\right)+\mathbf{b}
 $$
根据公式，更像是利用线性层或是矩阵（$\mathbf{U}^{T}$以及$\mathbf{V}^{T}$）分别与标签嵌入和特征图进行矩阵乘法，得到维度相同的矩阵，随后通过矩阵乘法以及bias将其转换为需要的维度，每一行为一个特征向量，对应一个标签的输出。整体拼成alignment matrix对齐矩阵

对于对齐矩阵的每一行分别进行softmax，在一个标签中进行操作。输出的标签表示label representation 就是对齐矩阵每一行分别与特征图的逐元素乘法，标签表示记为$f_i$

## 场景感知标签共现模型

将特征图池化以及线性化后，得到一组特征向量。

对于预先设定的K个场景，都有一个对应的出现概率矩阵（该矩阵更新策略在后面）。分别用上面的特征向量均值乘以每个场景的概率矩阵，将输出值softmax。最大值即为该图对应的场景。

概率矩阵计算：

首先计算统计矩阵，初始化为0，采用multi-hot编码，有目标即为1，没有为0，增加公式为：
$$
	\mathbf{C}^{s}=\mathbf{C}^{s}+\mathbf{y} \mathbf{y}^{T}
 $$
对于统计矩阵的每个位置，将除对角线外的元素除以对角线元素，得到概率矩阵。概率矩阵的对角线都设置为0.

## 图模型的应用

$f_i$作为图模型的一个初始值记为$h^0$，采用共概率矩阵与h矩阵进行乘法，得到对应的m矩阵表示节点之间的关系

通过门控循环单元进行h的更新，节点的最终输出对于标签以及图像同时进行了编码

## 损失函数

增加了一个辅助损失函数

$$
	\mathcal{L}_{2}=\sum_{k=0}^{K-1} \bar{\pi}_{k} \log \bar{\pi}_{k}-\log \frac{1}{K}
 $$
$\bar{\pi}_{k}$表示属于某个类别的概率。当输入每个类别的概率相同的时候，式子第一项取最小值。第二项为常数，与场景数有关。

另外一个损失函数为非对称焦损：
$$
	\mathcal{L}_{\text {cls }}=\frac{1}{n} \sum_{i=0}^{n-1}\left\{\begin{array}{cc}
\left(1-p_{i}\right)^{\gamma^{+}} \log p_{i}, & y_{i}=1 \\
p_{i}^{\gamma^{-}} \log \left(1-p_{i}\right), & y_{i}=0
\end{array}\right.
 $$
$\gamma^{+}$以及$\gamma^{-}$分别设为0和2

## 类别预测

将图模型输入以及最终输出进行concat，通过线性层转化为需要的大小，并进行sigmoid函数处理

# Experiment

## dataset

Pascal VOC 2007
NUS-WIDE
Microsoft COCO

## experimental setting

size：448\*448
80 epoch
图模型迭代次数为3

## effect

小幅提升

# Future

## problem

1. 图中特征图之后的池化和线性层应该在第一个特征提取模块，分别输入计算对齐矩阵共现概率矩阵、
2. 场景数目选取需要有对数据集的预先估计
