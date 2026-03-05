# 金融数据分析：P27：MAD法去极值演示 📊

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_0.png)

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_2.png)

在本节课中，我们将要学习数据预处理中的第二种去极值方法——MAD法。我们将详细解释其原理，并通过代码演示如何实现。

## 概述

上一节我们介绍了基于百分位的去极值方法。本节中我们来看看另一种稳健的方法：MAD法。MAD是“Median Absolute Deviation”（中位数绝对偏差）的缩写。这种方法通过数据自身的中位数和偏差来定义正常值的范围，对异常值不敏感，因此在处理包含极端值的数据时非常有效。

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_4.png)

## MAD法原理

MAD法的核心思想是：先计算数据序列的中位数，然后计算每个数据点与中位数之差的绝对值，再对这些绝对值序列求中位数，从而得到一个稳健的尺度估计。

以下是计算步骤：

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_6.png)

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_8.png)

1.  计算原始数据序列的中位数，记为 **Median**。
    *   公式：`Median = median(原始序列)`
2.  计算每个数据点与中位数的绝对偏差。
    *   公式：`绝对偏差序列 = |原始序列 - Median|`
3.  计算绝对偏差序列的中位数，得到MAD值。
    *   公式：`MAD = median(绝对偏差序列)`
4.  设定正常值的上下边界。通常使用一个常数 **N**（常取1.4826）乘以MAD值来扩展范围。
    *   上界公式：`上界 = Median + N * MAD`
    *   下界公式：`下界 = Median - N * MAD`
5.  将超出上下界的数据点视为极值，并进行处理（如缩尾或截断）。

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_10.png)

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_12.png)

## 代码实现

以下是使用Python和Pandas库实现MAD去极值函数的示例。

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_14.png)

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_16.png)

```python
import pandas as pd
import numpy as np

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_18.png)

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_20.png)

def filter_extreme_mad(series, n=1.4826):
    """
    使用MAD法对序列进行去极值处理。

    参数:
    series (pd.Series): 待处理的数据序列。
    n (float): 扩展系数，默认值为1.4826。

    返回:
    pd.Series: 处理后的数据序列。
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)

    # 2. 计算绝对偏差序列
    abs_deviation = (series - median).abs()

    # 3. 计算绝对偏差序列的中位数，即MAD值
    mad = abs_deviation.quantile(0.5)

    # 4. 计算上下界
    upper_bound = median + n * mad
    lower_bound = median - n * mad

    # 5. 对超出边界的数据进行缩尾处理
    filtered_series = series.clip(lower=lower_bound, upper=upper_bound)

    return filtered_series
```

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_22.png)

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_24.png)

## 函数使用与结果可视化

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_26.png)

定义了函数后，我们可以将其应用于实际数据，并可视化处理前后的对比。

以下是使用示例：

```python
# 假设 `data` 是包含待处理数据的Pandas Series
filtered_data = filter_extreme_mad(data, n=1.4826)

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_28.png)

# 可视化对比
import matplotlib.pyplot as plt

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_30.png)

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_32.png)

plt.figure(figsize=(10, 6))
plt.plot(data.values, label='原始数据', alpha=0.7)
plt.plot(filtered_data.values, label='MAD法处理后', alpha=0.9)
plt.legend()
plt.title('MAD法去极值效果对比')
plt.show()
```

执行代码后，蓝色线条代表原始数据，橙色线条代表经过MAD法处理后的数据。可以观察到，超出计算边界的极端值已被调整到边界处，而大部分正常数据保持不变。

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_34.png)

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_36.png)

## 总结

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_38.png)

![](img/5fa71eeffa6f33fbb9eebfbfbf9ea39a_40.png)

本节课中我们一起学习了MAD去极值法。我们首先理解了其计算原理，即通过中位数和绝对偏差中位数来稳健地估计数据的正常范围。接着，我们一步步实现了对应的Python函数，并演示了如何应用该函数处理数据以及可视化结果。MAD法是数据预处理中一种非常实用的工具，能有效降低极端值对后续分析的影响。