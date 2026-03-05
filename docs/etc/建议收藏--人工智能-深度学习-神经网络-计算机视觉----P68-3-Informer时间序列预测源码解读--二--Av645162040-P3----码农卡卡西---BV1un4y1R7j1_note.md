# Python机器学习与量化交易：P68：3. Informer时间序列预测源码解读（二）

在本节课中，我们将继续深入解读Informer模型的源码，重点关注数据预处理、时间特征提取以及模型前向传播的初始步骤。我们将以简单直白的方式，逐行分析代码逻辑，帮助初学者理解其核心思想。

![](img/6e3f5887a61a9770b674aeaafb7ca7e9_1.png)

## 数据标准化与时间特征提取

![](img/6e3f5887a61a9770b674aeaafb7ca7e9_3.png)

![](img/6e3f5887a61a9770b674aeaafb7ca7e9_5.png)

![](img/6e3f5887a61a9770b674aeaafb7ca7e9_7.png)

上一节我们介绍了数据加载的基本流程，本节中我们来看看数据是如何进行标准化和特征提取的。

首先，代码对数据进行标准化处理。这是机器学习中常见的预处理步骤，目的是将不同尺度的特征缩放到相似的范围内，有助于模型训练。

![](img/6e3f5887a61a9770b674aeaafb7ca7e9_9.png)

![](img/6e3f5887a61a9770b674aeaafb7ca7e9_11.png)

```python
# 使用 StandardScaler 对数据进行标准化
scaler = StandardScaler()
scaler.fit(data[train_data_start:train_data_end])
data_scaled = scaler.transform(data)
data_scaled = torch.from_numpy(data_scaled).float()
```

**核心步骤解析**：
1.  `fit`：计算训练数据的均值和标准差。
2.  `transform`：使用计算出的均值和标准差对所有数据进行标准化转换。
3.  `torch.from_numpy`：将NumPy数组转换为PyTorch张量，为后续模型计算做准备。

接下来，代码从数据中提取出时间列。在时间序列预测中，时间本身是极其重要的特征，蕴含着周期性、趋势性等信息。

```python
# 提取时间列
time_data = data_raw[['date']]
```

提取出时间数据后，需要将其转换为便于处理的格式，并从中提取有意义的特征。

```python
# 将时间字符串转换为 pandas 的 datetime 格式
time_stamps = pd.to_datetime(time_data.squeeze(), format='%Y-%m-%d %H:%M:%S')
# 提取时间特征
time_features = time_features(time_stamps, timeenc=1, freq='h')
```

`time_features` 函数是特征提取的核心。它默认提取四种时间特征，并将它们的值归一化到 `[-0.5, 0.5]` 的区间内。

以下是提取的四种特征：
*   **小时特征**：表示一天中的哪个小时，通过 `(hour / 23) - 0.5` 进行归一化。
*   **星期特征**：表示一周中的哪一天（周一至周日），通过 `(day_of_week / 6) - 0.5` 进行归一化。
*   **月份日期特征**：表示一个月中的哪一天，进行相应归一化。
*   **年份日期特征**：表示一年中的哪一天，进行相应归一化。

这种归一化处理是为了消除原始数值大小差异对模型的影响，使所有特征处于相近的尺度。

## 构建数据集与数据加载器

数据处理完毕后，需要将其封装成PyTorch可用的数据集（Dataset）和数据加载器（DataLoader）。

自定义数据集类的 `__getitem__` 方法是关键。在时间序列预测中，我们通常采用滑动窗口的方式构造样本。

```python
def __getitem__(self, index):
    # 计算序列的起始和结束位置
    s_begin = index
    s_end = s_begin + self.seq_len  # 输入序列长度，例如96
    r_begin = s_end - self.label_len  # 解码器已知部分起点
    r_end = r_begin + self.label_len + self.pred_len  # 解码器总序列终点

    # 获取输入序列 (seq_len)
    seq_x = self.data_x[s_begin:s_end]
    # 获取解码器输入序列 (label_len + pred_len)
    # 前 label_len 个是已知值，后 pred_len 个用0初始化
    seq_y = self.data_y[r_begin:r_end]
    # 获取对应的时间特征
    seq_x_mark = self.data_stamp[s_begin:s_end]
    seq_y_mark = self.data_stamp[r_begin:r_end]

    return seq_x, seq_y, seq_x_mark, seq_y_mark
```

**参数说明**：
*   `seq_len`：输入编码器（Encoder）的历史序列长度。
*   `label_len`：输入解码器（Decoder）的已知序列长度，这部分是有真实标签的。
*   `pred_len`：需要预测的未来序列长度，在Decoder输入中用0填充。

例如，若 `seq_len=96`, `label_len=48`, `pred_len=24`，则一个样本的构造如下：
*   `seq_x`：取96个时间步的数据作为Encoder输入。
*   `seq_y`：共72个时间步（48+24）。前48个是紧接 `seq_x` 的真实值，提供给Decoder作为已知信息；后24个用0填充，是模型需要预测的部分。

DataLoader会批量调用 `__getitem__` 方法，组装成 `(batch_size, seq_len, feature_size)` 等形状的张量，供模型训练使用。

## 模型前向传播初步：Embedding

数据准备就绪后，进入模型的前向传播过程。Informer模型基于Transformer架构，第一步是对输入进行嵌入（Embedding）。

对于编码器（Encoder）的输入，嵌入层需要融合三种信息：

![](img/6e3f5887a61a9770b674aeaafb7ca7e9_13.png)

![](img/6e3f5887a61a9770b674aeaafb7ca7e9_15.png)

```python
# DataEmbedding 类中的 forward 方法（简化示意）
def forward(self, x, x_mark):
    # x: 序列数据 [batch_size, seq_len, feature_dim]
    # x_mark: 时间特征 [batch_size, seq_len, time_feature_dim]

    # 1. 对原始特征进行嵌入（使用1D卷积）
    x = self.value_embedding(x)  # 例如将 feature_dim 从12投影到512

    # 2. 加入位置编码 (Positional Encoding)
    x = x + self.position_embedding(x)

    # 3. 加入时间特征嵌入
    x = x + self.temporal_embedding(x_mark)  # 将时间特征也投影到512维

    return x
```

**三种嵌入的融合**：
1.  **值嵌入**：将原始的多元特征（例如12维）通过一个一维卷积层（或线性层）投影到高维空间（例如512维）。公式可以表示为：`x_embed = Conv1d(x)`。
2.  **位置嵌入**：为序列中的每个位置添加一个固定的或可学习的编码向量，让模型感知元素的顺序。公式为：`x = x + PE(pos)`。
3.  **时间特征嵌入**：将提取的归一化时间特征（例如4维）也投影到与值嵌入相同的维度（512维），然后相加。公式为：`x = x + Embedding(time_feature)`。

通过相加的方式，模型同时考虑了数据本身的值、其在序列中的位置以及所属的时间上下文信息。解码器（Decoder）的输入也会经过一个类似的嵌入过程。

## 进入编码器与注意力机制

完成嵌入后，数据被送入编码器堆栈。编码器由多个 `EncoderLayer` 组成，每个层包含一个核心的多头概率稀疏自注意力机制（ProbSparse Self-Attention）和一个前馈网络。

![](img/6e3f5887a61a9770b674aeaafb7ca7e9_17.png)

![](img/6e3f5887a61a9770b674aeaafb7ca7e9_19.png)

我们首先关注注意力机制。在标准的Transformer中，注意力计算需要 `Query (Q)`, `Key (K)`, `Value (V)` 三个矩阵。

![](img/6e3f5887a61a9770b674aeaafb7ca7e9_21.png)

![](img/6e3f5887a61a9770b674aeaafb7ca7e9_23.png)

```python
# 在 AttentionLayer 中
def forward(self, queries, keys, values):
    B, L, _ = queries.shape
    _, S, _ = keys.shape
    H = self.n_heads

    # 将输入线性投影到 Q, K, V
    queries = self.query_projection(queries).view(B, L, H, -1)
    keys = self.key_projection(keys).view(B, S, H, -1)
    values = self.value_projection(values).view(B, S, H, -1)

    # 执行核心的注意力计算
    out, attn = self.inner_attention(queries, keys, values)
    out = out.view(B, L, -1)
    return self.out_projection(out), attn
```

![](img/6e3f5887a61a9770b674aeaafb7ca7e9_25.png)

![](img/6e3f5887a61a9770b674aeaafb7ca7e9_27.png)

这里，`queries`, `keys`, `values` 最初都是同一个输入 `x`（即经过嵌入后的序列）。通过三个不同的线性投影层（`query_projection`, `key_projection`, `value_projection`），得到 `Q`, `K`, `V` 矩阵。`self.inner_attention` 就是实现Informer核心创新点——**概率稀疏注意力**——的地方。

![](img/6e3f5887a61a9770b674aeaafb7ca7e9_29.png)

本节课中我们一起学习了Informer源码中数据预处理、时间特征工程、数据集构建以及模型前向传播开始部分的关键步骤。我们看到了如何将原始时间序列数据标准化并提取丰富的时序特征，也了解了模型输入是如何通过嵌入层融合多种信息的。下一节，我们将深入剖析Informer最核心的**概率稀疏自注意力机制**的代码实现，理解它是如何高效地选择关键`Query`并进行计算的。