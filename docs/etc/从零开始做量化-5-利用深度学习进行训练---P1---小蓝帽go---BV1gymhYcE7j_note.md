# 从零开始做量化｜5：利用深度学习进行训练 - P1

![](img/d5e0409fe29bb60709a69ec7761a2af2_1.png)

在本节课中，我们将学习如何构建和训练一个用于量化交易的深度学习模型。我们将详细介绍模型的核心结构、数据处理方法以及损失函数的设计思路。

![](img/d5e0409fe29bb60709a69ec7761a2af2_3.png)

---

## 模型训练概览

![](img/d5e0409fe29bb60709a69ec7761a2af2_5.png)

我们打开 `train.py` 文件。

![](img/d5e0409fe29bb60709a69ec7761a2af2_7.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_1.png)

可以看到代码中包含了 `preprocess`、`DATASET`、`device` 和 `data loader` 等部分。

![](img/d5e0409fe29bb60709a69ec7761a2af2_9.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_3.png)

`DATASET` 和 `data loader` 是 PyTorch 中用于加载数据集的机制。

![](img/d5e0409fe29bb60709a69ec7761a2af2_5.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_11.png)

这里不展开细说。

![](img/d5e0409fe29bb60709a69ec7761a2af2_7.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_13.png)

由于我使用的是 Mac 电脑，且训练数据量很大，因此需要对数据集进行特殊处理。我编写了一个 `separate` 函数，用于将大数据集进行切分。

![](img/d5e0409fe29bb60709a69ec7761a2af2_15.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_9.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_17.png)

具体做法是，先打乱整个数据集，然后分段加载到内存中。

![](img/d5e0409fe29bb60709a69ec7761a2af2_19.png)

![](img/d5e0409fe29bb60709a2af2_11.png)

你可以根据自己的计算环境编写 `data loader` 和 `DATASET`，也可以直接使用我的代码。

![](img/d5e0409fe29bb60709a69ec7761a2af2_21.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_13.png)

这里不先展开。

![](img/d5e0409fe29bb60709a69ec7761a2af2_23.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_15.png)

接下来，我们来看模型最终的结构部分。

![](img/d5e0409fe29bb60709a69ec7761a2af2_17.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_25.png)

## 模型核心结构

我们先忽略 `attention` 部分，来看一下 `sequence model`。

![](img/d5e0409fe29bb60709a69ec7761a2af2_27.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_19.png)

这是整个量化模型的主要框架。

![](img/d5e0409fe29bb60709a69ec7761a2af2_29.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_21.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_31.png)

在这个部分，我首先定义了一个 **LSTM** 层。

```python
self.lstm = nn.LSTM(input_size, hidden_size, ...)
```

![](img/d5e0409fe29bb60709a69ec7761a2af2_33.png)

它的作用是对股票时间序列特征进行提取。这些序列特征是我们之前用阿尔法因子，在定长的时间窗口内取出并拼接而成的。

![](img/d5e0409fe29bb60709a69ec7761a2af2_23.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_35.png)

像 `innocence`、`industry` 和 `our` 这类特征，则是通过 **NLP** 方法，将上市公司的介绍、股票指数、交易时间等内容，通过高维嵌入的方式加入到模型中。

![](img/d5e0409fe29bb60709a69ec7761a2af2_25.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_37.png)

最后是一个 **MLP**，即多层感知机。

```python
self.mlp = nn.Sequential(
    nn.Linear(...),
    nn.ReLU(),
    ...
)
```

![](img/d5e0409fe29bb60709a69ec7761a2af2_39.png)

它的作用是将所有特征进行拼接，并进行非线性计算。

![](img/d5e0409fe29bb60709a69ec7761a2af2_27.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_41.png)

模型最终输出一个多分类的结果。

![](img/d5e0409fe29bb60709a69ec7761a2af2_43.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_29.png)

### 前向传播流程

![](img/d5e0409fe29bb60709a69ec7761a2af2_45.png)

在 `forward` 函数中，定义了模型的计算流程。

![](img/d5e0409fe29bb60709a69ec7761a2af2_31.png)

首先，获取通过 NLP 得到的 `embedding` 特征。

![](img/d5e0409fe29bb60709a69ec7761a2af2_47.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_33.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_49.png)

然后，对序列特征进行 LSTM 特征提取。

![](img/d5e0409fe29bb60709a69ec7761a2af2_51.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_35.png)

接着，我在这里增加了一个 **Attention** 自注意力机制。

```python
# 简化的注意力计算
attention_weights = torch.softmax(scores, dim=-1)
context = torch.matmul(attention_weights, value)
```

![](img/d5e0409fe29bb60709a69ec7761a2af2_37.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_53.png)

加入 Attention 有两个原因：
1.  LSTM 虽然是长短期记忆网络，但其长距离上下文关联能力可能还不够强。
2.  Attention 可以增加动态权重的特征提取能力，即不同特征在相同位置上的权重可以不同。

![](img/d5e0409fe29bb60709a69ec7761a2af2_39.png)
![](img/d5e0409fe29bb60709a69ec7761a2af2_41.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_55.png)

然后，我们将所有特征进行拼接，包括：
*   `embedding` 特征
*   LSTM 的输出结果
*   Attention 的输出结果

![](img/d5e0409fe29bb60709a69ec7761a2af2_43.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_57.png)

将 LSTM 和 Attention 的结果结合，也体现了残差连接的思想。

![](img/d5e0409fe29bb60709a69ec7761a2af2_45.png)
![](img/d5e0409fe29bb60709a69ec7761a2af2_47.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_59.png)

最后，通过 MLP 对拼接后的特征进行非线性计算，并输出多分类结果。

![](img/d5e0409fe29bb60709a69ec7761a2af2_49.png)
![](img/d5e0409fe29bb60709a69ec7761a2af2_51.png)

## 损失函数设计

上一节我们介绍了模型的结构，本节我们来看看如何设计损失函数来指导模型学习。整个模型的损失函数设计得比较复杂，它由两部分组成。

![](img/d5e0409fe29bb60709a69ec7761a2af2_61.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_53.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_63.png)

以下是损失函数的两个核心部分：

1.  **Focal Loss**
    *   这部分用于处理数据不平衡的分类问题。我额外增加了一个 `batch_alpha` 的概念，目的是进一步加强对数据量较少类别（例如，涨得好的股票）的学习能力。

2.  **Distance Loss**
    *   这部分是我自己构思的，不一定完全正确。我认为股票标签（0,1,2,3）的预测不是一个简单的分类问题，而是一个有序回归问题。
    *   例如，如果模型同时给“3”（涨得最好）和“0”（跌）这两个标签很高的预测概率，这显然是矛盾的。我会对这种预测施加惩罚。
    *   反之，如果模型给相邻的标签（如“2”和“3”，或“0”和“1”）较高的概率，我认为是可以接受的。因为上一节中打标签的方式比较绝对，多分类可能会损失一些信息，这个机制旨在弥补这一点。
    *   这个设计可能后续会更改或优化。虽然标签设置参考了一些论文，但我感觉仍有改进空间，因此额外增加了这个损失项。

![](img/d5e0409fe29bb60709a69ec7761a2af2_65.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_55.png)
![](img/d5e0409fe29bb60709a69ec7761a2af2_57.png)
![](img/d5e0409fe29bb60709a69ec7761a2af2_59.png)
![](img/d5e0409fe29bb60709a69ec7761a2af2_61.png)
![](img/d5e0409fe29bb60709a69ec7761a2af2_63.png)
![](img/d5e0409fe29bb60709a69ec7761a2af2_65.png)
![](img/d5e0409fe29bb60709a69ec7761a2af2_67.png)

---

![](img/d5e0409fe29bb60709a69ec7761a2af2_67.png)

## 总结

本节课中，我们一起学习了量化交易深度学习模型的训练第一部分。我们了解了模型的核心结构，它结合了 LSTM、Attention 和 MLP 来提取时序与文本特征。我们还探讨了为处理数据不平衡和标签有序性而设计的复合损失函数。在下一节中，我们将利用 GBDT 等方法来对整个量化模型进行进一步的优化。

![](img/d5e0409fe29bb60709a69ec7761a2af2_69.png)

![](img/d5e0409fe29bb60709a69ec7761a2af2_69.png)