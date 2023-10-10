 Multi-Label Self-Supervised Learning with Scene Images
日期：2023-10-10  时间：10:49

# keywords

Multi-label, self-supervised, scene image

# Background

自监督学习是十分必要的

如果直接进行场景图象匹配可能存在匹配不准确的问题

# Method

将场景图像自监督学习任务认为是一种多标签分类任务，类似于prompt learning

![[Multi-Label Self-Supervised Learning with Scene Images.png]]

在自监督学习中，原始图像和经过处理的图像之间作为标签和样本进行学习。将标签那一组认为是一个字典，保存多组信息，分别对应多标签任务中的不同标签。

随后使用binary cross-entropy loss进行训练，得到的模型通过微调应用至下游任务

# Future

## problem

1. 如果只使用BCE的话会出现不稳定的情况，需要使用InfoNCE与BCE进行结合才能得到良好的效果
2. 这个多标签分类中，字典的每一个标签并不表示一个实际的类别，或者一个具有明显特征的区域，与人眼实际理解的不同标签是不一样的

## improvement

1. 探索只是用BCE维持稳定的方法
2. 自监督转换为多标签后的多标签的可解释性
