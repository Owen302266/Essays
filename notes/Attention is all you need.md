
 Attention is all you need
日期：2023-09-12  时间：16:53

# keywords

transformer, attention

# Background

文字生成任务

# Method

![[Attention is all you need 1.png]]

## 输入编码

### input embedding

根据需要输入大小构造一个字典，每个单词或者字母认为是字典中的一个元素。将对应出的编码投影为需要的大小的一维数组（512）

### positional encoding

位置编码根据该单词的位置进行编号，为pos=0， 1， 。。。；

根据input embedding 的一维数组的大小（512），按照下面公式分别进行编码
$$
\begin{aligned}
P E_{(p o s, 2 i)} & =\sin \left(\operatorname{pos} / 10000^{2 i / d_{\text {model }}}\right) \\
P E_{(p o s, 2 i+1)} & =\cos \left(p o s / 10000^{2 i / d_{\text {model }}}\right)
\end{aligned}
 $$

### 整体编码

input embedding + positional encoding = input encoding整体编码

## 注意力机制

![[Attention is all you need 2.png]]


Q：query查询

K：key键

V：value值

三者的矩阵维度是一致的，假设为（6， 512）

### 单头注意力

Q×K：（6， 512） × （6， 512） = （6， 6）
将该值除以\sqrt d（\sqrt512）
对该输出进行softmax，得到不同位置对应的权重

将该输出乘V： （6， 6） × （6， 512） = （6， 512）与输入维度一致

意味着只进行了额外计算，没有改变输入输出的值，可以随意拼接

### 多头注意力


# Experiment

文本翻译任务，效果提升
# Future

将文本输入改为图像输入，应用至图像分类（VIT）
