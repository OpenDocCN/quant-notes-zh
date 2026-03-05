# Python金融量化：P30：MAD法去极值演示

![](img/6ad404991fe85044ff7df28373f88e0a_0.png)

![](img/6ad404991fe85044ff7df28373f88e0a_2.png)

在本节课中，我们将要学习第二种数据去极值的方法——MAD法。我们将详细解释其原理，并通过代码演示如何实现。

## 概述

上一节我们介绍了百分位法去极值，本节中我们来看看另一种方法：MAD法。MAD是“Median Absolute Deviation”（中位数绝对偏差）的缩写。虽然其公式看起来稍显复杂，但计算过程实际上相当直接。它的核心思想是利用数据的中位数和绝对偏差来设定一个合理的范围，从而识别并处理异常值。

![](img/6ad404991fe85044ff7df28373f88e0a_4.png)

## MAD法原理

MAD法的操作步骤如下：

![](img/6ad404991fe85044ff7df28373f88e0a_6.png)

首先，对于一个给定的数据序列，我们计算它的中位数。假设我们有一个序列 `[1, 2, 3, 4, 5, 6, 7]`，其中位数是 `4`。

![](img/6ad404991fe85044ff7df28373f88e0a_8.png)

接下来，我们计算序列中每个数值与中位数的**绝对差值**。以下是计算过程：
*   对于数值 `1`：`|1 - 4| = 3`
*   对于数值 `2`：`|2 - 4| = 2`
*   对于数值 `3`：`|3 - 4| = 1`
*   对于数值 `4`：`|4 - 4| = 0`
*   以此类推...

![](img/6ad404991fe85044ff7df28373f88e0a_10.png)

![](img/6ad404991fe85044ff7df28373f88e0a_12.png)

这样，我们得到了一个新的序列 `[3, 2, 1, 0, ...]`。

![](img/6ad404991fe85044ff7df28373f88e0a_14.png)

然后，我们对这个由绝对差值构成的新序列**再次求中位数**。这个最终得到的中位数，就是所谓的 **MAD值**。

![](img/6ad404991fe85044ff7df28373f88e0a_16.png)

![](img/6ad404991fe85044ff7df28373f88e0a_18.png)

得到MAD值后，我们就可以设定数据的正常范围。范围的上界和下界由以下公式决定：
*   **上界 = 中位数 + N × MAD值**
*   **下界 = 中位数 - N × MAD值**

![](img/6ad404991fe85044ff7df28373f88e0a_20.png)

![](img/6ad404991fe85044ff7df28373f88e0a_22.png)

其中，**N** 是一个常数，通常取值为 **1.4826**。任何超出 `[下界， 上界]` 这个范围的数据点，都将被视为异常值并进行处理（例如，缩放到边界值）。

![](img/6ad404991fe85044ff7df28373f88e0a_24.png)

![](img/6ad404991fe85044ff7df28373f88e0a_26.png)

这种方法本质上与百分位法类似，都是通过寻找两个边界来界定数据的合理范围，只是确定边界的方法不同。

![](img/6ad404991fe85044ff7df28373f88e0a_28.png)

## 代码实现

![](img/6ad404991fe85044ff7df28373f88e0a_30.png)

![](img/6ad404991fe85044ff7df28373f88e0a_32.png)

理解了原理后，我们来看看如何用Python代码实现MAD去极值。代码逻辑完全遵循上述步骤。

![](img/6ad404991fe85044ff7df28373f88e0a_34.png)

以下是实现MAD去极值功能的函数：

![](img/6ad404991fe85044ff7df28373f88e0a_36.png)

![](img/6ad404991fe85044ff7df28373f88e0a_38.png)

```python
def filter_extreme_mad(series, n):
    """
    使用MAD法对序列进行去极值处理。
    
    参数:
    series: pandas Series，待处理的数据序列。
    n: float，用于计算边界的倍数，通常为1.4826。
    
    返回:
    series: 处理后的数据序列。
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)
    
    # 2. 计算每个数据点与中位数的绝对偏差，并求该偏差序列的中位数，即MAD值
    mad = (series - median).abs().quantile(0.5)
    
    # 3. 计算上界和下界
    high = median + n * mad
    low = median - n * mad
    
    # 4. 将超出边界的数据裁剪到边界
    series = series.clip(lower=low, upper=high)
    
    return series
```

![](img/6ad404991fe85044ff7df28373f88e0a_40.png)

现在，让我们使用这个函数来处理数据。我们传入数据序列和一个N值（这里使用常规值1.4826）来执行去极值操作。

![](img/6ad404991fe85044ff7df28373f88e0a_42.png)

![](img/6ad404991fe85044ff7df28373f88e0a_44.png)

```python
# 假设 `data_series` 是您的原始数据
n_value = 1.4826
filtered_data_mad = filter_extreme_mad(data_series, n_value)

![](img/6ad404991fe85044ff7df28373f88e0a_46.png)

![](img/6ad404991fe85044ff7df28373f88e0a_48.png)

# 可以绘制图表对比处理前后的数据（绘图非本节重点，仅作演示）
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 6))
plt.plot(data_series.values, label=‘原始数据‘, alpha=0.7)
plt.plot(filtered_data_mad.values, label=‘MAD法处理后‘, alpha=0.7)
plt.legend()
plt.show()
```

![](img/6ad404991fe85044ff7df28373f88e0a_50.png)

执行代码后，`filtered_data_mad` 就是经过MAD法处理后的数据。在对比图中，超出蓝色范围（即`[下界， 上界]`）的极端值会被调整到边界上。

![](img/6ad404991fe85044ff7df28373f88e0a_52.png)

![](img/6ad404991fe85044ff7df28373f88e0a_54.png)

## 总结

![](img/6ad404991fe85044ff7df28373f88e0a_56.png)

![](img/6ad404991fe85044ff7df28373f88e0a_58.png)

本节课中我们一起学习了第二种去极值方法——MAD法。我们首先了解了它的计算原理，即通过数据的中位数和绝对偏差的中位数来动态确定数据的合理范围。接着，我们一步步实现了对应的Python函数，并演示了如何使用它来处理实际数据。MAD法是对百分位法的一种补充，它为识别和处理异常值提供了另一个有效的工具。