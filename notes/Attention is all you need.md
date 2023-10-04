
 Attention is all you need
日期：2023-09-12  时间：16:53

# keywords

transformer, attention

# Background

文字生成任务

# Method

![[Attention is all you need 1.png]]


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

Q，K，V都先分别经过不同的线性化矩阵，输入输出矩阵维度保持不变，目的是注意力机制侧重于不同的位置

将Q， K， V分别裁剪为h个向量，对应位置的三个Q，K，V分别进行单头注意力计算

将所有h个输出按位置concat起来

论文中全部使用的是多头注意力，根据Q，K，V是否相同的值分为自注意力以及交叉注意力

## 编码器

###  输入编码

#### input embedding

根据需要输入大小构造一个字典，每个单词或者字母认为是字典中的一个元素。将对应出的编码投影为需要的大小的一维数组（512）

#### positional encoding

位置编码根据该单词的位置进行编号，为pos=0， 1， 。。。；

根据input embedding 的一维数组的大小（512），按照下面公式分别进行编码
$$
\begin{aligned}
P E_{(p o s, 2 i)} & =\sin \left(\operatorname{pos} / 10000^{2 i / d_{\text {model }}}\right) \\
P E_{(p o s, 2 i+1)} & =\cos \left(p o s / 10000^{2 i / d_{\text {model }}}\right)
\end{aligned}
 $$

#### 整体编码

input embedding + positional encoding = input encoding整体编码

### 自注意力机制

对于编码器的每个子部分，输入输出维度一致。只进行了自注意力机制

将输入复制为4份，三份作为Q，K，V进行自注意力计算，自注意力输出与剩下一份进行加法运算并正则化

正则化结果输入前馈网络输出仍然维度保持一致

整个过程重复N次

## 解码器

### 编码过程

与input embedding一致

### mask过程

输入时要保证前序输入与后续输入之间无关，所以输入时加一个mask，矩阵对角线右侧的设为inf，softmax后为0

### 交叉注意力

Q，K从编码器处输入，V是经过mask的输出，其余与自注意力机制一样

整个过程重复N次

## 训练

encoder：sos+sentence+eos

decoder：sentence + eos

## 测试

encoder：sos+sentence+eos

decoder：sos，迭代输出，每次输出下一个单词，直到输出eos结束

# Experiment

文本翻译任务，效果提升
# Future

将文本输入改为图像输入，应用至图像分类（VIT）
