# Python金融分析与量化交易实战教程：P28：03-3-MAD法去极值演示

![](img/74d20d562b6d3c7b38c2bd20bd16687a_0.png)

![](img/74d20d562b6d3c7b38c2bd20bd16687a_2.png)

在本节课中，我们将要学习数据预处理中的第二种去极值方法——MAD法。我们将详细解释其原理，并通过代码演示如何实现。

## 概述

上一节我们介绍了基于百分位的去极值方法，本节中我们来看看另一种稳健的方法——MAD法。MAD法通过计算数据的中位数绝对偏差来识别异常值，其核心思想是利用数据自身的分布特性来设定合理的上下边界。

![](img/74d20d562b6d3c7b38c2bd20bd16687a_4.png)

## MAD法原理

MAD法的计算过程分为几个步骤。首先，我们需要计算原始数据序列的中位数。假设我们有一个数据序列，例如 `[1, 2, 3, 4, 5, 6, 7]`，其中位数为 `4`。

接下来，我们计算每个数据点与中位数之差的绝对值，形成一个新的序列。例如，对于数据点 `1`，其绝对偏差为 `|1 - 4| = 3`。对所有数据点进行此操作后，我们得到一个新的绝对偏差序列。

然后，我们计算这个绝对偏差序列的中位数，这个值就是MAD值。它代表了数据围绕中位数的典型离散程度。

![](img/74d20d562b6d3c7b38c2bd20bd16687a_6.png)

![](img/74d20d562b6d3c7b38c2bd20bd16687a_8.png)

最后，我们利用中位数和MAD值来设定数据的正常范围。通常，上界设定为 `中位数 + N * MAD`，下界设定为 `中位数 - N * MAD`。其中，`N` 是一个常数，通常取值为 `1.4826`。任何落在这个范围之外的数据点，将被视为极值并进行处理。

以下是核心公式：

![](img/74d20d562b6d3c7b38c2bd20bd16687a_10.png)

![](img/74d20d562b6d3c7b38c2bd20bd16687a_12.png)

1.  计算中位数：`median = median(series)`
2.  计算绝对偏差序列：`abs_deviation = |series - median|`
3.  计算MAD值：`MAD = median(abs_deviation)`
4.  设定边界：
    *   上界：`upper_bound = median + N * MAD`
    *   下界：`lower_bound = median - N * MAD`

## 代码实现

![](img/74d20d562b6d3c7b38c2bd20bd16687a_14.png)

![](img/74d20d562b6d3c7b38c2bd20bd16687a_16.png)

理解了原理后，我们来看看如何用Python代码实现MAD去极值。我们将编写一个函数，其逻辑与上述步骤完全一致。

![](img/74d20d562b6d3c7b38c2bd20bd16687a_18.png)

![](img/74d20d562b6d3c7b38c2bd20bd16687a_20.png)

```python
import pandas as pd
import numpy as np

![](img/74d20d562b6d3c7b38c2bd20bd16687a_22.png)

def filter_extreme_mad(series, n=1.4826):
    """
    使用MAD法对序列进行去极值处理。
    参数:
        series: 待处理的数据序列 (pandas Series)
        n: 用于计算边界的倍数，默认为1.4826
    返回:
        处理后的数据序列
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)

    # 2. 计算每个数据点与中位数之差的绝对值
    abs_deviation = (series - median).abs()

    # 3. 计算绝对偏差序列的中位数，即MAD值
    mad = abs_deviation.quantile(0.5)

    # 4. 计算上下边界
    upper_bound = median + n * mad
    lower_bound = median - n * mad

    # 5. 将超出边界的数据替换为边界值
    filtered_series = series.clip(lower=lower_bound, upper=upper_bound)

    return filtered_series
```

![](img/74d20d562b6d3c7b38c2bd20bd16687a_24.png)

![](img/74d20d562b6d3c7b38c2bd20bd16687a_26.png)

以下是函数中关键步骤的说明：

*   `series.quantile(0.5)`：用于计算序列的中位数。
*   `(series - median).abs()`：计算每个元素与中位数的绝对偏差。
*   `.clip(lower=lower_bound, upper=upper_bound)`：这是一个非常方便的方法，它会将序列中所有小于下界的值设置为下界，所有大于上界的值设置为上界，而中间的值保持不变。

## 方法应用与对比

现在，让我们应用这个函数来处理数据，并可视化结果，以便直观地理解MAD法的效果。

![](img/74d20d562b6d3c7b38c2bd20bd16687a_28.png)

```python
# 假设 `original_data` 是您的原始数据序列 (pandas Series)
filtered_data_mad = filter_extreme_mad(original_data, n=1.4826)

![](img/74d20d562b6d3c7b38c2bd20bd16687a_30.png)

# 可视化对比
import matplotlib.pyplot as plt

![](img/74d20d562b6d3c7b38c2bd20bd16687a_32.png)

plt.figure(figsize=(10, 6))
plt.plot(original_data.index, original_data.values, label='原始数据', alpha=0.7)
plt.plot(filtered_data_mad.index, filtered_data_mad.values, label='MAD法处理后', linewidth=2)
plt.legend()
plt.title('MAD法去极值效果对比')
plt.xlabel('索引')
plt.ylabel('数值')
plt.show()
```

![](img/74d20d562b6d3c7b38c2bd20bd16687a_34.png)

执行代码后，您将看到一张图表。图中，原始数据可能包含一些尖锐的峰值（极值），而经过MAD法处理后的数据曲线会更加平滑，那些异常的峰值被限制在了由中位数和MAD计算出的合理边界之内。

![](img/74d20d562b6d3c7b38c2bd20bd16687a_36.png)

## 总结

![](img/74d20d562b6d3c7b38c2bd20bd16687a_38.png)

![](img/74d20d562b6d3c7b38c2bd20bd16687a_40.png)

本节课中我们一起学习了MAD去极值法。我们首先了解了其计算原理，它通过两次计算中位数来得到一个稳健的数据离散度估计（MAD），进而设定数据的正常范围。然后，我们一步步实现了对应的Python函数，并应用该函数处理数据，通过图表对比直观展示了MAD法平滑数据、抑制极值的效果。与百分位法相比，MAD法对极端值不敏感，因此在实际数据分布未知或存在异常值时更为稳健。