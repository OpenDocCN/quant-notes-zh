# 量化感知训练（QAT）：2：量化基础与后训练量化

![](img/a6fb6ff7c7296f6f724810511c557b16_1.png)

![](img/a6fb6ff7c7296f6f724810511c557b16_3.png)

在本节课中，我们将学习神经网络量化的基本概念，并动手实现一个简单的后训练量化（PTQ）过程。我们将从理解量化的动机开始，逐步学习如何将浮点数表示的权重和激活值转换为整数表示。

![](img/a6fb6ff7c7296f6f724810511c557b16_5.png)

![](img/a6fb6ff7c7296f6f724810511c557b16_7.png)

## 概述

![](img/a6fb6ff7c7296f6f724810511c557b16_9.png)

量化是一种将神经网络中的浮点数参数（如权重和激活值）转换为低精度整数（如8位整数）的技术。其主要目的是**减小模型体积**和**加速推理过程**。本节课将首先介绍量化的必要性，然后通过一个简单的例子，演示如何对一个训练好的模型进行后训练量化。

![](img/a6fb6ff7c7296f6f724810511c557b16_11.png)

上一节我们介绍了量化的基本概念，本节中我们来看看如何具体实现一个量化过程。

![](img/a6fb6ff7c7296f6f724810511c557b16_13.png)

![](img/a6fb6ff7c7296f6f724810511c557b16_15.png)

## 为何需要量化？

![](img/a6fb6ff7c7296f6f724810511c557b16_17.png)

量化可以将模型从32位浮点数表示转换为8位整数表示。这能显著**减小模型体积**，使其更适合部署在存储和计算资源有限的设备上，例如移动设备或嵌入式系统。

## 一个简单的示例网络

![](img/a6fb6ff7c7296f6f724810511c557b16_19.png)

为了演示量化过程，我们使用一个在MNIST数据集上训练好的简单卷积神经网络。该网络结构如下：

以下是网络的核心结构：
*   两个卷积层
*   两个全连接层

![](img/a6fb6ff7c7296f6f724810511c557b16_21.png)

该网络在MNIST测试集上达到了约99%的准确率。我们将以此作为基准，观察量化后的精度变化。

![](img/a6fb6ff7c7296f6f724810511c557b16_23.png)

## 后训练量化（PTQ）原理

![](img/a6fb6ff7c7296f6f724810511c557b16_24.png)

后训练量化是指在模型训练完成后，直接对其权重和激活值进行量化，而不需要重新训练。其核心思想是将浮点数值映射到整数范围（例如0-255）。

![](img/a6fb6ff7c7296f6f724810511c557b16_26.png)

![](img/a6fb6ff7c7296f6f724810511c557b16_28.png)

量化与反量化的过程可以通过以下公式描述：

![](img/a6fb6ff7c7296f6f724810511c557b16_30.png)

**量化公式**：
`quantized_tensor = round(float_tensor / scale) + zero_point`

![](img/a6fb6ff7c7296f6f724810511c557b16_32.png)

**反量化公式**：
`dequantized_float_tensor = (quantized_tensor - zero_point) * scale`

![](img/a6fb6ff7c7296f6f724810511c557b16_34.png)

其中：
*   **`scale`（缩放因子）**：一个浮点数，决定了浮点数到整数的映射比例。计算公式通常为 `(max_value - min_value) / (quant_max - quant_min)`。
*   **`zero_point`（零点）**：一个整数，代表浮点数0在量化后的整数值。

![](img/a6fb6ff7c7296f6f724810511c557b16_36.png)

## 实现量化函数

现在，我们根据上述原理来实现量化函数。在代码中，我们使用一个命名元组 `QTensor` 来方便地存储量化后的张量、缩放因子和零点。

![](img/a6fb6ff7c7296f6f724810511c557b16_38.png)

以下是 `QTensor` 的定义和量化函数：
```python
from collections import namedtuple

# 定义一个命名元组来存储量化张量及其参数
QTensor = namedtuple('QTensor', ['tensor', 'scale', 'zero_point'])

![](img/a6fb6ff7c7296f6f724810511c557b16_40.png)

def quantize_tensor(x, num_bits=8):
    """
    量化张量函数
    Args:
        x: 输入浮点张量
        num_bits: 量化位数，默认为8
    Returns:
        QTensor: 包含量化后整数张量、scale和zero_point的命名元组
    """
    qmin = 0
    qmax = 2 ** num_bits - 1

    min_val, max_val = x.min(), x.max()

    scale = (max_val - min_val) / (qmax - qmin)
    zero_point = qmin - min_val / scale

    # 确保zero_point在整数范围内
    if zero_point < qmin:
        zero_point = qmin
    elif zero_point > qmax:
        zero_point = qmax
    else:
        zero_point = round(zero_point)

    q_x = zero_point + x / scale
    q_x.clamp_(qmin, qmax).round_()
    q_x = q_x.round().byte()  # 转换为8位整数类型

    return QTensor(tensor=q_x, scale=scale, zero_point=zero_point)
```

## 修改网络前向传播

![](img/a6fb6ff7c7296f6f724810511c557b16_42.png)

为了实现量化推理，我们需要修改网络的前向传播函数。在每一层计算前，对其输入（激活）和权重进行量化，在计算完成后，再将结果反量化为浮点数进行后续操作。

以下是修改前向传播的核心步骤：
1.  量化输入激活值。
2.  量化该层的权重。
3.  使用量化后的整数进行卷积或矩阵乘法计算。
4.  将整数结果反量化为浮点数。
5.  添加偏置（如果需要）并应用激活函数。

![](img/a6fb6ff7c7296f6f724810511c557b16_44.png)

![](img/a6fb6ff7c7296f6f724810511c557b16_46.png)

这个过程确保了在前向传播中，核心计算（如 `conv2d` 或 `linear`）是在低精度整数上执行的。

![](img/a6fb6ff7c7296f6f724810511c557b16_48.png)

![](img/a6fb6ff7c7296f6f724810511c557b16_50.png)

## 总结

![](img/a6fb6ff7c7296f6f724810511c557b16_52.png)

本节课中我们一起学习了神经网络量化的基础。我们首先理解了量化技术**减小模型体积和加速推理**的核心价值。然后，我们深入探讨了**后训练量化（PTQ）** 的原理，即通过 `scale` 和 `zero_point` 将浮点数映射到整数。最后，我们动手实现了一个量化函数，并了解了如何通过修改网络前向传播来集成量化计算。虽然我们演示的是最简单的后训练量化，但这是理解更复杂的量化感知训练（QAT）的重要基础。