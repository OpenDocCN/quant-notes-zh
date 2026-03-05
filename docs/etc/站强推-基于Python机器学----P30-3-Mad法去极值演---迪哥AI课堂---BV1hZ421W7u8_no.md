# 金融量化分析：P30：MAD法去极值演示 📊

![](img/65a7661e520e3c930decaa6856bb5359_0.png)

![](img/65a7661e520e3c930decaa6856bb5359_2.png)

在本节课中，我们将要学习数据预处理中的第二种去极值方法——MAD法。我们将详细解释其原理，并通过Python代码演示如何实现。

## 概述

上一节我们介绍了基于百分位数的去极值方法。本节中我们来看看另一种稳健的去极值方法：MAD法。MAD是“Median Absolute Deviation”（中位数绝对偏差）的缩写，它通过数据的中位数和偏差来识别异常值，对极端值不敏感，因此非常稳健。

![](img/65a7661e520e3c930decaa6856bb5359_4.png)

## MAD法原理

MAD法的核心思想是：先计算数据序列的中位数，然后计算每个数据点与中位数之差的绝对值，再对这些绝对值序列求中位数，从而得到一个稳健的尺度估计。

以下是计算MAD值并确定正常值范围的步骤：

![](img/65a7661e520e3c930decaa6856bb5359_6.png)

1.  **计算原始数据的中位数**：
    对于一个数据序列，首先计算其中位数 `median`。

![](img/65a7661e520e3c930decaa6856bb5359_8.png)

2.  **计算绝对偏差**：
    计算序列中每个数据点与中位数 `median` 的绝对差值。公式为：
    `absolute_deviation = |data_point - median|`

![](img/65a7661e520e3c930decaa6856bb5359_10.png)

![](img/65a7661e520e3c930decaa6856bb5359_12.png)

3.  **计算MAD值**：
    对步骤2得到的绝对偏差序列，再次计算其中位数，这个值就是MAD。

![](img/65a7661e520e3c930decaa6856bb5359_14.png)

4.  **确定正常值范围**：
    根据中位数 `median` 和MAD值，设定正常数据的上下界。通常的公式是：
    `上界 = median + n * MAD`
    `下界 = median - n * MAD`
    其中，`n` 是一个常数，通常取 **1.4826**。任何超出此范围的数据点将被视为极值并进行处理（如缩尾或截断）。

![](img/65a7661e520e3c930decaa6856bb5359_16.png)

![](img/65a7661e520e3c930decaa6856bb5359_18.png)

## 代码实现

![](img/65a7661e520e3c930decaa6856bb5359_20.png)

![](img/65a7661e520e3c930decaa6856bb5359_22.png)

理解了原理后，我们来看看如何用Python代码实现MAD法去极值。

![](img/65a7661e520e3c930decaa6856bb5359_24.png)

以下是实现MAD去极值功能的函数：

![](img/65a7661e520e3c930decaa6856bb5359_26.png)

![](img/65a7661e520e3c930decaa6856bb5359_28.png)

```python
import pandas as pd
import numpy as np

def filter_extreme_MAD(series, n):
    """
    使用MAD法对序列进行去极值处理。
    参数:
        series: 待处理的数据序列 (pandas Series)
        n: 用于计算边界的倍数，通常为1.4826
    返回:
        处理后的数据序列
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)

    # 2. 计算每个数据点与中位数的绝对偏差
    abs_deviation = (series - median).abs()

    # 3. 计算绝对偏差的中位数，即MAD值
    MAD = abs_deviation.quantile(0.5)

    # 4. 计算上下界
    upper_bound = median + n * MAD
    lower_bound = median - n * MAD

    # 5. 对超出边界的数据进行缩尾处理
    filtered_series = series.clip(lower=lower_bound, upper=upper_bound)

    return filtered_series
```

![](img/65a7661e520e3c930decaa6856bb5359_30.png)

![](img/65a7661e520e3c930decaa6856bb5359_32.png)

## 函数使用演示

![](img/65a7661e520e3c930decaa6856bb5359_34.png)

现在，我们可以使用这个函数来处理实际数据。

```python
# 假设我们有一个名为 `data_series` 的数据序列
# 调用MAD去极值函数，n取常用值1.4826
filtered_data = filter_extreme_MAD(data_series, n=1.4826)

![](img/65a7661e520e3c930decaa6856bb5359_36.png)

![](img/65a7661e520e3c930decaa6856bb5359_38.png)

# 可以绘制图像对比处理前后的数据
import matplotlib.pyplot as plt

![](img/65a7661e520e3c930decaa6856bb5359_40.png)

![](img/65a7661e520e3c930decaa6856bb5359_42.png)

plt.figure(figsize=(10, 5))
plt.subplot(1, 2, 1)
plt.plot(data_series.values, label='原始数据')
plt.title('原始数据')
plt.legend()

![](img/65a7661e520e3c930decaa6856bb5359_44.png)

plt.subplot(1, 2, 2)
plt.plot(filtered_data.values, label='MAD法处理后', color='orange')
plt.title('MAD法去极值后')
plt.legend()
plt.show()
```

![](img/65a7661e520e3c930decaa6856bb5359_46.png)

![](img/65a7661e520e3c930decaa6856bb5359_48.png)

执行上述代码后，蓝色线条代表原始数据，橙色线条代表经过MAD法处理后的数据。可以看到，极端值被有效地限制在了合理的范围内。

![](img/65a7661e520e3c930decaa6856bb5359_50.png)

## 总结

![](img/65a7661e520e3c930decaa6856bb5359_52.png)

![](img/65a7661e520e3c930decaa6856bb5359_54.png)

本节课中我们一起学习了MAD去极值法。我们首先了解了其基于中位数和绝对偏差的稳健原理，然后逐步实现了计算MAD值、确定边界以及处理数据的完整代码。MAD法是金融数据分析中处理异常值的一种有效工具，它不依赖于数据的具体分布，对极端值有很好的鲁棒性。