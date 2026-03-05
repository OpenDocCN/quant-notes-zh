# Python机器学习与量化交易：P69：4. Informer时间序列预测源码解读（三）

![](img/ea61f15b03d53d6a4a4b6914c56319de_1.png)

在本节课中，我们将继续深入解读Informer模型的源码，重点关注其核心的注意力机制——ProbSparse自注意力。我们将详细分析其如何通过采样和筛选来加速计算并聚焦于序列中的关键信息点。

---

![](img/ea61f15b03d53d6a4a4b6914c56319de_3.png)

## 概述

上一节我们介绍了Informer模型的数据预处理和整体架构。本节中，我们来看看模型最核心的创新点：**ProbSparse自注意力机制**。该机制旨在解决传统Transformer在长序列预测中计算复杂度高的问题，通过筛选出最重要的查询（Q）和键（K）进行计算，从而大幅提升效率。

## ProbSparse自注意力机制详解

传统的自注意力计算需要序列中每个元素与其他所有元素进行交互，计算复杂度为序列长度的平方。Informer提出了一种“稀疏化”策略，只计算少数重要的注意力对。

![](img/ea61f15b03d53d6a4a4b6914c56319de_5.png)

### 核心思想

其核心思想是：在一个长序列中，并非所有时间点都同等重要。例如，在温度预测中，季节转换的“拐点”比平稳时期包含更多信息。模型的目标是自动找出这些信息量大的“关键”时间点。

### 代码实现流程

以下是该机制在代码中的关键步骤分解。

#### 步骤一：初始化Q, K, V

首先，模型通过线性变换从输入特征中生成查询（Q）、键（K）和值（V）矩阵。它们的初始维度是相同的。

```python
# 假设输入x的维度为 [batch_size, seq_len, d_model]
# 经过线性层后得到Q, K, V
q = self.W_q(x)  # 维度: [batch_size, seq_len, d_k]
k = self.W_k(x)  # 维度: [batch_size, seq_len, d_k]
v = self.W_v(x)  # 维度: [batch_size, seq_len, d_v]
```
在我们的例子中，序列长度（seq_len）为96，每个头的维度（d_k）为64。

![](img/ea61f15b03d53d6a4a4b6914c56319de_7.png)

#### 步骤二：在键（K）上进行随机采样

与传统方法不同，ProbSparse注意力并非直接在查询（Q）上筛选，而是先对键（K）进行随机采样。这是因为要评估一个查询（Q）是否“重要”，需要看它与所有键（K）交互的分布情况。

![](img/ea61f15b03d53d6a4a4b6914c56319de_9.png)

![](img/ea61f15b03d53d6a4a4b6914c56319de_11.png)

![](img/ea61f15b03d53d6a4a4b6914c56319de_13.png)

具体操作是为每个查询（Q）随机选择固定数量（例如25个）的键（K）进行计算，而不是全部的96个。这相当于为每个“人生故事”随机抽取25个“章节”来快速判断其是否精彩。

![](img/ea61f15b03d53d6a4a4b6914c56319de_15.png)

```python
# 对K进行维度扩充，便于后续采样
k_expanded = k.unsqueeze(2).expand(-1, -1, num_random_samples, -1)
# 生成随机索引，为每个Q选择25个K
random_indices = torch.randint(0, seq_len, (batch_size, seq_len, num_random_samples))
# 根据索引采集K
k_sampled = k_expanded.gather(1, random_indices.unsqueeze(-1).expand(-1, -1, -1, d_k))
```
采样后，`k_sampled`的维度变为 `[batch_size, seq_len, 25, d_k]`。

#### 步骤三：计算“重要性得分”

对于每个查询Qi，我们用其与采样出的25个键Kj计算注意力分数。然后，我们关注其**得分最高的那一次交互**（即最“精彩”的章节）。

1.  **计算最大值**：取Qi与25个Kj的注意力分数中的最大值。
2.  **计算与平均的差异**：将这个最大值与所有查询得分的平均值进行比较。差异越大，说明这个查询Qi越“与众不同”，信息量越大。

```python
# 计算每个Q与采样K的注意力分数（简化表示）
attn_scores = torch.matmul(q, k_sampled.transpose(-2, -1)) / sqrt(d_k)
# 取每个Q对应的最大分数
max_scores = attn_scores.max(dim=-1)[0]
# 计算所有Q得分的平均值
mean_scores = attn_scores.mean(dim=-1).mean(dim=-1, keepdim=True)
# 计算重要性得分：最大值与平均值的差异
importance_scores = max_scores - mean_scores
```
`importance_scores` 的维度为 `[batch_size, seq_len]`，代表了96个查询中每个查询的“重要程度”。

![](img/ea61f15b03d53d6a4a4b6914c56319de_17.png)

![](img/ea61f15b03d53d6a4a4b6914c56319de_19.png)

#### 步骤四：筛选Top-k查询

根据计算出的重要性得分，我们筛选出得分最高的前k个（例如25个）查询。这些被认为是序列中最关键的信息点。

![](img/ea61f15b03d53d6a4a4b6914c56319de_21.png)

```python
# 选择重要性得分最高的25个Q的索引
topk_indices = importance_scores.topk(k=num_top_queries, dim=-1)[1]
# 根据索引从原始Q中取出这些重要的Q
q_topk = q.gather(1, topk_indices.unsqueeze(-1).expand(-1, -1, d_k))
```
现在，`q_topk` 的维度变为 `[batch_size, 25, d_k]`。

![](img/ea61f15b03d53d6a4a4b6914c56319de_23.png)

![](img/ea61f15b03d53d6a4a4b6914c56319de_25.png)

#### 步骤五：计算精简后的注意力

![](img/ea61f15b03d53d6a4a4b6914c56319de_27.png)

![](img/ea61f15b03d53d6a4a4b6914c56319de_29.png)

现在，我们用筛选出的25个重要查询（Q_topk）与**原始的、完整的96个键（K）** 计算注意力。这一步是模型获取全局信息的关键。

![](img/ea61f15b03d53d6a4a4b6914c56319de_31.png)

![](img/ea61f15b03d53d6a4a4b6914c56319de_33.png)

```python
# 计算精简后的注意力矩阵
attn_weights = torch.softmax(torch.matmul(q_topk, k.transpose(-2, -1)) / sqrt(d_k), dim=-1)
# 注意力权重与值（V）相乘，得到上下文向量
context = torch.matmul(attn_weights, v)
```
得到的 `context` 维度为 `[batch_size, 25, d_v]`，它只包含了25个重要位置的更新后信息。

![](img/ea61f15b03d53d6a4a4b6914c56319de_35.png)

#### 步骤六：处理未选中的查询

那剩下的71个（96-25）未被选中的“平庸”查询如何处理？论文采用了一种简洁的策略：**用所有值（V）向量的平均值来更新它们**。这意味着这些位置用全局的、平均的信息来表征。

![](img/ea61f15b03d53d6a4a4b6914c56319de_37.png)

![](img/ea61f15b03d53d6a4a4b6914c56319de_39.png)

```python
# 计算V在序列长度维度上的均值，得到一个“平均向量”
v_mean = v.mean(dim=1, keepdim=True)  # 维度: [batch_size, 1, d_v]
# 初始化一个全由平均向量组成的上下文矩阵
context_full = v_mean.expand(-1, seq_len, -1)  # 维度: [batch_size, 96, d_v]
# 将25个重要位置的更新结果填充回去
context_full.scatter_(1, topk_indices.unsqueeze(-1).expand(-1, -1, d_v), context)
```
最终，`context_full` 的维度恢复为 `[batch_size, 96, d_v]`，其中25个位置是精细计算的结果，其余71个位置是全局平均向量。

![](img/ea61f15b03d53d6a4a4b6914c56319de_41.png)

### 残差连接与层归一化

![](img/ea61f15b03d53d6a4a4b6914c56319de_43.png)

与标准Transformer相同，注意力模块的输出会经过残差连接、层归一化和前馈网络。

![](img/ea61f15b03d53d6a4a4b6914c56319de_45.png)

```python
output = x + self.dropout(context_full)  # 残差连接
output = self.norm1(output)
output = output + self.dropout(self.ffn(output)) # 前馈网络 + 残差
output = self.norm2(output)
```

## 编码器中的“蒸馏”操作

Informer的编码器由多个上述的注意力层堆叠而成。为了进一步压缩序列长度、突出重要信息并加速计算，在层与层之间加入了**“蒸馏”**操作。

![](img/ea61f15b03d53d6a4a4b6914c56319de_47.png)

具体实现是使用一维最大池化（MaxPooling）对序列长度进行下采样。

```python
self.distill = nn.Sequential(
    nn.Conv1d(in_channels=d_model, out_channels=d_model, kernel_size=3),
    nn.MaxPool1d(kernel_size=3, stride=2) # 步长为2，序列长度减半
)
```
例如，第一层输入序列长度为96，经过池化后第二层的输入长度变为48。相应地，在第二层的ProbSparse注意力中，采样的数量（如25）也会按比例调整（例如变为12或10）。这样层层递进，形成了一个金字塔形的结构，高效地提炼了序列信息。

## 解码器结构

解码器同样由多层组成，每层包含两种注意力：
1.  **Masked ProbSparse自注意力**：让解码器在预测时只能看到当前及之前时刻的信息，不能看到未来（通过掩码矩阵实现）。
2.  **ProbSparse交叉注意力**：让解码器的查询（Q）与编码器最后一层的输出（作为K和V）进行交互，从而将编码器提取的关键历史信息注入到解码过程中。

![](img/ea61f15b03d53d6a4a4b6914c56319de_49.png)

![](img/ea61f15b03d53d6a4a4b6914c56319de_51.png)

解码器的输入由两部分拼接而成：
*   **已知序列部分**：历史序列的后一段，作为解码的起始。
*   **待预测部分**：用零填充的占位符，表示要预测的未来时间点。

解码器的最终输出通过一个线性投影层，映射到预测的维度（例如，预测未来24个时间点，每个时间点有12个特征值）。

## 总结

![](img/ea61f15b03d53d6a4a4b6914c56319de_53.png)

本节课我们一起深入学习了Informer源码的核心——ProbSparse自注意力机制。我们了解到它通过**在键（K）上随机采样来评估查询（Q）的重要性**，进而筛选出少量关键查询进行计算，并结合**均值更新策略**处理非关键部分，从而实现了计算效率的显著提升。此外，编码器的**蒸馏结构**进一步压缩信息，解码器则通过**掩码自注意力和交叉注意力**完成预测。理解这一机制是掌握Informer模型的关键，也为处理其他长序列预测问题提供了宝贵的思路。