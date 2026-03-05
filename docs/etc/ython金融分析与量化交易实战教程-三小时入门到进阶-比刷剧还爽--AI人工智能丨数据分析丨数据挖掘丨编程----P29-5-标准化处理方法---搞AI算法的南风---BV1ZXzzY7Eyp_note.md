# Python金融分析与量化交易实战教程：P29：5-标准化处理方法 📊

![](img/ddcd7221379011e84a44cb4cd21ffba0_1.png)

在本节课中，我们将要学习数据预处理中一个至关重要的步骤——标准化。标准化是数据挖掘和机器学习中常用的技术，旨在消除不同数据特征因量纲和取值范围不同而带来的影响，使数据更易于分析和建模。

![](img/ddcd7221379011e84a44cb4cd21ffba0_3.png)

上一节我们介绍了数据的基本处理，本节中我们来看看如何通过标准化来统一数据的尺度。

## 标准化原理

标准化操作的核心目标是让不同维度的数据具有可比性。我们通过一个简单的例子来理解其必要性。

假设我们有一组数据，包含两个特征：X1和X2。X1的取值范围较大，而X2的取值范围较小。如果直接使用这些原始数据进行计算，取值范围大的特征（如X1）可能会在模型中占据主导地位，从而掩盖了取值范围小的特征（如X2）的作用。为了公平地看待每一个特征，我们需要进行标准化。

标准化的数学公式如下：

**`z = (x - μ) / σ`**

其中：
*   **`x`** 是原始数据。
*   **`μ`** 是该特征所有数据的平均值（均值）。
*   **`σ`** 是该特征所有数据的标准差。

这个公式可以分解为两个步骤：

1.  **去均值**：`x - μ`。这一步的目的是将数据集的中心移动到零点。处理后，数据在各个维度上将以零为中心对称分布。
2.  **除标准差**：`(x - μ) / σ`。这一步的目的是调整数据的“伸缩”范围。标准差反映了数据的离散程度。对于取值范围大、数据分散的特征，其标准差也大，相除后能将其数值范围压缩。反之，对于取值范围集中的特征，其标准差小，相除后能将其数值范围适度放大。最终目的是使所有特征的数据分布在一个近似的、较小的数值区间内。

通过这两个步骤，标准化后的数据具有以下特点：均值为0，标准差为1。

## 代码实现

理解了原理后，我们可以动手实现标准化的函数。以下是使用Python和Pandas库实现标准化的方法。

首先，我们需要计算数据的均值和标准差。

```python
import pandas as pd

# 假设我们有一个Pandas Series数据
data = pd.Series([...]) # 你的数据

![](img/ddcd7221379011e84a44cb4cd21ffba0_5.png)

# 计算均值和标准差
mean_val = data.mean()
std_val = data.std()
```

![](img/ddcd7221379011e84a44cb4cd21ffba0_7.png)

接下来，我们根据标准化公式进行计算。

```python
def standardize(series):
    """
    对输入的Pandas Series进行标准化处理。
    参数:
        series: 待处理的Pandas Series数据。
    返回:
        标准化后的Pandas Series数据。
    """
    # 计算均值和标准差
    mean_val = series.mean()
    std_val = series.std()
    # 应用标准化公式
    standardized_series = (series - mean_val) / std_val
    return standardized_series
```

![](img/ddcd7221379011e84a44cb4cd21ffba0_9.png)

![](img/ddcd7221379011e84a44cb4cd21ffba0_11.png)

现在，我们可以使用这个函数来处理数据。

![](img/ddcd7221379011e84a44cb4cd21ffba0_13.png)

```python
# 原始数据示例
original_data = pd.Series([30, 25, 100, 45, 60])
print("原始数据:")
print(original_data)

# 进行标准化
standardized_data = standardize(original_data)
print("\n标准化后的数据:")
print(standardized_data)
```

![](img/ddcd7221379011e84a44cb4cd21ffba0_15.png)

执行上述代码后，你会发现原本取值范围差异很大的数据（如30到100），经过标准化后，其数值被转换到了一个相对较小的、以零为中心的区间内（例如-1.5到1.5之间）。

![](img/ddcd7221379011e84a44cb4cd21ffba0_17.png)

![](img/ddcd7221379011e84a44cb4cd21ffba0_19.png)

## 其他方法与工具

除了手动实现，市面上也有成熟的工具包可以一键完成标准化，这在实际项目中非常方便。

以下是使用`scikit-learn`库进行标准化的方法：

![](img/ddcd7221379011e84a44cb4cd21ffba0_21.png)

```python
from sklearn.preprocessing import StandardScaler
import numpy as np

![](img/ddcd7221379011e84a44cb4cd21ffba0_23.png)

# 准备数据，注意sklearn通常处理二维数组
data_array = original_data.values.reshape(-1, 1)

![](img/ddcd7221379011e84a44cb4cd21ffba0_25.png)

![](img/ddcd7221379011e84a44cb4cd21ffba0_27.png)

# 创建标准化器并拟合数据
scaler = StandardScaler()
scaler.fit(data_array)

![](img/ddcd7221379011e84a44cb4cd21ffba0_29.png)

# 转换数据
standardized_array = scaler.transform(data_array)
print(standardized_array.flatten()) # 转换为一维数组查看
```

![](img/ddcd7221379011e84a44cb4cd21ffba0_31.png)

![](img/ddcd7221379011e84a44cb4cd21ffba0_33.png)

`scikit-learn`的`StandardScaler`功能更强大，能方便地处理整个数据集（DataFrame），并保存拟合参数用于后续新数据的转换。

![](img/ddcd7221379011e84a44cb4cd21ffba0_35.png)

## 总结

![](img/ddcd7221379011e84a44cb4cd21ffba0_37.png)

本节课中我们一起学习了数据标准化处理。我们首先理解了标准化的目的：消除不同特征间量纲和取值范围差异的影响。随后，我们深入分析了标准化的核心公式 **`z = (x - μ) / σ`**，并将其分解为“去均值”和“除标准差”两个关键步骤。最后，我们不仅手动实现了标准化函数，还介绍了利用`scikit-learn`库快速完成标准化的方法。标准化是数据预处理中的基础且关键的一步，掌握它能为后续的金融分析与建模打下坚实的基础。