# 量化感知训练（3）：自定义卷积层

在本节课中，我们将学习如何编写一个自定义的量化感知卷积层。这个自定义层基于标准的卷积层，但在执行卷积操作前，会对权重进行特定的量化处理。由于量化操作（如四舍五入）本身不可微，我们将使用代理梯度来解决反向传播的问题。

## 概述与目标 🎯

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_1.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_3.png)

上一节我们介绍了量化感知训练的基本概念。本节中，我们来看看如何实现一个具体的自定义量化卷积层。我们的目标是创建一个名为 `CustomConv2d` 的层，其核心功能是：在进行卷积计算前，先将权重乘以 `2**N`，然后四舍五入取整，最后再执行标准的卷积操作。

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_5.png)

## 核心概念与实现思路 💡

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_6.png)

由于 `round`（四舍五入）函数在数学上不可微，它会在计算图中引入断点，导致梯度无法回传。为了解决这个问题，我们需要使用**代理梯度**。一个简单且常用的代理梯度策略是使用 `y = x`，即在反向传播时，假装量化操作没有发生，梯度直接通过。

以下是实现该自定义层需要包含的关键部分：
*   **初始化函数 (`__init__`)**: 定义层的参数，如输入/输出通道数、卷积核大小等。
*   **前向传播函数 (`forward`)**: 定义数据从输入到输出的计算过程，包括量化权重。
*   **反向传播函数 (`backward`)**: 定义梯度计算。在PyTorch中，我们通常使用 `torch.autograd.Function` 来定制需要特殊梯度处理的操作。

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_8.png)

## 代码实现 🛠️

现在，让我们根据上述思路，使用 PyTorch 来实现这个自定义层。

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_10.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_11.png)

首先，我们需要导入必要的库。

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_13.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_15.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_17.png)

```python
import torch
import torch.nn as nn
import torch.nn.functional as F
from torch.autograd import Function
```

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_18.png)

接下来，我们定义核心的量化函数及其对应的代理梯度函数。我们将这个组合操作定义为一个自定义的 `torch.autograd.Function`。

```python
class RoundQuantize(Function):
    """
    自定义自动微分函数：
    前向传播：对输入执行 乘以2^N -> 四舍五入 的操作。
    反向传播：使用代理梯度，直接返回输入的梯度。
    """
    @staticmethod
    def forward(ctx, input, n):
        # 前向传播：量化操作
        scale = 2 ** n
        output = torch.round(input * scale) / scale  # 等价于 round(input*scale)/scale
        return output

    @staticmethod
    def backward(ctx, grad_output):
        # 反向传播：代理梯度策略 (y=x的梯度为1)
        # 直接将输出梯度传递给输入梯度
        grad_input = grad_output.clone()
        return grad_input, None  # 对输入`input`的梯度，对参数`n`的梯度为None
```

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_20.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_22.png)

有了这个基础函数后，我们就可以构建完整的自定义卷积层了。

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_24.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_26.png)

```python
class CustomConv2d(nn.Module):
    """
    自定义量化感知卷积层。
    在卷积前对权重进行 (乘以2^N -> 四舍五入) 的量化。
    """
    def __init__(self, in_channels, out_channels, kernel_size, stride=1, padding=0, n=4):
        super(CustomConv2d, self).__init__()
        # 初始化可学习的卷积权重参数
        self.weight = nn.Parameter(torch.randn(out_channels, in_channels, kernel_size, kernel_size))
        # 初始化量化参数 N，可以固定，也可以设为可学习参数
        self.n = n
        # 保存卷积超参数
        self.stride = stride
        self.padding = padding

    def forward(self, x):
        # 在前向传播中，对权重应用量化函数
        quantized_weight = RoundQuantize.apply(self.weight, self.n)
        # 使用量化后的权重执行卷积操作
        output = F.conv2d(x, quantized_weight, stride=self.stride, padding=self.padding)
        return output
```

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_28.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_29.png)

## 代码解析与要点说明 📝

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_31.png)

以上代码定义了一个可用的自定义层。以下是关键点的解析：

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_33.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_34.png)

1.  **`RoundQuantize` 类**：这个类继承自 `torch.autograd.Function`，它允许我们分别定义前向和反向计算逻辑。在 `forward` 中，我们执行了 `input * (2**n) -> round -> / (2**n)` 的量化过程。在 `backward` 中，我们采用了最简单的代理梯度，即直接返回输出的梯度 `grad_output` 作为输入的梯度 `grad_input`。

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_36.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_37.png)

2.  **`CustomConv2d` 类**：这个类继承自 `nn.Module`。在 `__init__` 中，我们使用 `nn.Parameter` 注册了可学习的权重 `self.weight`。在 `forward` 函数中，我们首先调用 `RoundQuantize.apply` 对权重进行量化，然后使用 PyTorch 的函数式卷积 `F.conv2d` 完成计算。

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_38.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_39.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_40.png)

3.  **代理梯度的位置**：代理梯度的逻辑明确地写在 `RoundQuantize.backward` 方法中。当PyTorch进行反向传播时，会自动调用此方法来计算量化操作的梯度。

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_41.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_42.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_43.png)

4.  **关于乘以 `2**N`**：在 `RoundQuantize.forward` 中，`scale = 2 ** n` 实现了“乘以2的N次方”。整个操作 `torch.round(input * scale) / scale` 等价于先放大、取整、再缩回原尺度，这正是要求的量化步骤。

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_44.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_45.png)

## 潜在问题与讨论 🤔

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_46.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_47.png)

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_48.png)

使用这个自定义层进行训练时，需要注意以下几点：

*   **训练速度**：由于引入了自定义的操作，训练速度可能会比使用原生层稍慢。
*   **梯度误差**：代理梯度 `y=x` 是一种近似，它会引入误差，可能影响模型的收敛速度和最终精度。在实际应用中，可能需要根据任务调整代理梯度策略。
*   **量化参数 `N`**：本例中将 `N` 设为固定值（如4）。`N` 的大小决定了量化的粒度（`scale`）。`N` 越大，量化间隔 `1/(2**N)` 越小，量化越精细，但也可能更容易受到舍入误差的影响。你可以尝试将其设置为可学习的参数，让模型在训练中自动寻找合适的量化尺度。
*   **性能验证**：在实际使用中，需要像训练普通模型一样，在训练集和验证集上监控损失和精度，以评估此量化层的有效性。

## 总结 🎓

![](img/e3a4f7a6a7b8fa4ec83181761234db2b_50.png)

本节课中我们一起学习了如何实现一个自定义的量化感知卷积层。我们首先明确了目标：创建一个在卷积前对权重进行缩放和取整量化的层。接着，我们理解了使用**代理梯度**来解决量化操作不可微问题的必要性。然后，我们通过定义 `RoundQuantize` 这个自定义自动微分函数来实现量化和其梯度计算，并最终将其嵌入到 `CustomConv2d` 模块中。通过本教程，你不仅学会了如何实现一个特定功能的层，也掌握了在PyTorch中处理不可微操作、定制反向传播流程的基本方法。