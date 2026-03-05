# Python金融分析量化交易：3：MAD法去极值演示

![](img/cd931e44074bc5fad46415305ec03153_0.png)

![](img/cd931e44074bc5fad46415305ec03153_2.png)

在本节课中，我们将要学习第二种数据去极值的方法——MAD法。我们将详细讲解其计算原理，并通过代码演示如何实现它。

## 概述

![](img/cd931e44074bc5fad46415305ec03153_4.png)

上一节我们介绍了百分位法去极值，本节中我们来看看另一种基于中位数绝对偏差的方法，即MAD法。这种方法通过计算数据的中位数和绝对偏差来确定数据的合理范围，从而识别并处理异常值。

## MAD法原理

MAD法的核心思想是计算一个名为“中位数绝对偏差”的值，并用它来设定数据的上下边界。其计算过程分为两步。

![](img/cd931e44074bc5fad46415305ec03153_6.png)

以下是MAD值的计算步骤：
1.  首先，计算原始数据序列的中位数。
2.  然后，计算序列中每个数据点与该中位数的绝对差值，形成一个新的差值序列。
3.  最后，对这个差值序列再次求中位数，得到的结果就是MAD值。

![](img/cd931e44074bc5fad46415305ec03153_8.png)

用公式可以表示为：
**MAD = median(|X_i - median(X)|)**
其中，`X` 代表原始数据序列，`median` 表示求中位数。

![](img/cd931e44074bc5fad46415305ec03153_10.png)

![](img/cd931e44074bc5fad46415305ec03153_12.png)

## 确定边界与处理数据

![](img/cd931e44074bc5fad46415305ec03153_14.png)

得到MAD值后，我们就可以设定数据的正常范围边界。通常，上界和下界通过以下公式确定：
**上界 = median(X) + N * MAD**
**下界 = median(X) - N * MAD**

![](img/cd931e44074bc5fad46415305ec03153_16.png)

![](img/cd931e44074bc5fad46415305ec03153_18.png)

这里的 `N` 是一个常数，通常取值为 **1.4826**。设定好边界后，处理数据的方法与百分位法类似：将超出上界的数据替换为上界值，将低于下界的数据替换为下界值。

![](img/cd931e44074bc5fad46415305ec03153_20.png)

![](img/cd931e44074bc5fad46415305ec03153_22.png)

![](img/cd931e44074bc5fad46415305ec03153_24.png)

## 代码实现

![](img/cd931e44074bc5fad46415305ec03153_26.png)

![](img/cd931e44074bc5fad46415305ec03153_28.png)

接下来，我们按照上述原理，用Python代码实现MAD去极值函数。

```python
import pandas as pd
import numpy as np

![](img/cd931e44074bc5fad46415305ec03153_30.png)

![](img/cd931e44074bc5fad46415305ec03153_32.png)

def filter_extreme_mad(series, n):
    """
    使用MAD法对序列进行去极值处理。
    :param series: 待处理的数据序列（pd.Series）
    :param n: 用于计算边界的倍数，通常为1.4826
    :return: 处理后的数据序列
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)

    # 2. 计算中位数绝对偏差 (MAD)
    # 先计算每个数据点与中位数的绝对差值
    abs_diff = (series - median).abs()
    # 再对绝对差值序列求中位数，得到MAD值
    mad = abs_diff.quantile(0.5)

    # 3. 利用MAD值计算上下边界
    upper_limit = median + n * mad
    lower_limit = median - n * mad

    # 4. 根据边界对数据进行裁剪
    filtered_series = series.clip(lower=lower_limit, upper=upper_limit)

    return filtered_series
```

![](img/cd931e44074bc5fad46415305ec03153_34.png)

## 函数应用演示

![](img/cd931e44074bc5fad46415305ec03153_36.png)

现在，我们可以使用这个函数来处理实际数据。

![](img/cd931e44074bc5fad46415305ec03153_38.png)

![](img/cd931e44074bc5fad46415305ec03153_40.png)

```python
# 假设 `data_series` 是您的原始数据序列（例如股票收益率）
# 通常 n 取 1.4826
n_value = 1.4826
filtered_data = filter_extreme_mad(data_series, n_value)

![](img/cd931e44074bc5fad46415305ec03153_42.png)

![](img/cd931e44074bc5fad46415305ec03153_44.png)

# 可以绘制图表对比处理前后的数据（此处为示意）
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 5))
plt.plot(data_series.values, label='原始数据', alpha=0.7)
plt.plot(filtered_data.values, label='MAD去极值后', alpha=0.9)
plt.legend()
plt.show()
```

![](img/cd931e44074bc5fad46415305ec03153_46.png)

执行以上代码后，蓝色线条（原始数据）中超出合理范围的部分将被“拉回”到橙色线条（处理后的数据）所表示的边界内。

![](img/cd931e44074bc5fad46415305ec03153_48.png)

![](img/cd931e44074bc5fad46415305ec03153_50.png)

## 总结

![](img/cd931e44074bc5fad46415305ec03153_52.png)

![](img/cd931e44074bc5fad46415305ec03153_54.png)

本节课中我们一起学习了MAD去极值法。我们首先理解了MAD值的计算过程，即先求原始数据中位数，再求各数据点与中位数差值的绝对值序列的中位数。然后，我们学会了如何利用MAD值和特定倍数 `N` 来确定数据的上下边界，并对异常值进行处理。最后，我们通过具体的Python函数实现了整个流程。MAD法是对中位数和偏差的稳健估计，是金融数据分析中处理异常值的有效工具之一。