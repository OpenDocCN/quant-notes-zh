# 量化交易实战课程：P30：MAD法去极值演示 🧮

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_0.png)

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_2.png)

在本节课中，我们将要学习第二种数据去极值的方法——MAD法。上一节我们介绍了百分位法，本节中我们来看看如何利用中位数绝对偏差来识别和处理异常值。

## 概述

MAD法是一种基于数据中位数和绝对偏差的稳健去极值方法。它的核心思想是通过计算数据点与中位数偏差的中位数，来确定一个合理的范围，并将超出此范围的值视为异常值。

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_4.png)

## MAD法原理

MAD法的计算过程分为几个步骤。

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_6.png)

以下是计算MAD值并确定边界的步骤：

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_8.png)

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_10.png)

1.  **计算原始数据的中位数**：首先，对于一个给定的数据序列，计算其中位数（Median）。
    *   公式：`median = Median(series)`

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_12.png)

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_14.png)

2.  **计算绝对偏差**：计算序列中每个数据点与中位数的绝对差值。
    *   公式：`abs_deviation = |series - median|`

3.  **计算MAD值**：计算上一步得到的绝对偏差序列的中位数，这个值就是中位数绝对偏差（Median Absolute Deviation, MAD）。
    *   公式：`MAD = Median(abs_deviation)`

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_16.png)

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_18.png)

4.  **确定边界**：根据中位数、MAD值和一个缩放系数N，计算数据的正常值范围上下界。
    *   上界公式：`upper_bound = median + N * MAD`
    *   下界公式：`lower_bound = median - N * MAD`

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_20.png)

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_22.png)

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_24.png)

5.  **处理极值**：将原始数据中超出 `[lower_bound, upper_bound]` 范围的值替换为边界值（或进行其他处理，如设为NaN），其逻辑与百分位法类似。

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_26.png)

其中，缩放系数N通常取一个经验值 **3**（或近似值3.0）。关于MAD法，大家了解其基本操作流程即可。

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_28.png)

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_30.png)

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_32.png)

## 代码实现

接下来，我们按照上述原理，用Python代码实现MAD去极值函数。

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_34.png)

```python
import pandas as pd
import numpy as np

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_36.png)

def filter_extreme_MAD(series, n=3):
    """
    使用MAD法对序列进行去极值处理。
    
    参数:
    series (pd.Series): 待处理的数据序列。
    n (float): 缩放系数，默认为3。
    
    返回:
    pd.Series: 处理后的数据序列。
    """
    # 1. 计算中位数
    median = series.quantile(0.5)
    
    # 2. 计算绝对偏差序列并求其中位数，得到MAD值
    mad = (series - median).abs().quantile(0.5)
    
    # 3. 计算上下界
    upper_bound = median + n * mad
    lower_bound = median - n * mad
    
    # 4. 对超出边界的数据进行截断处理
    filtered_series = series.clip(lower=lower_bound, upper=upper_bound)
    
    return filtered_series
```

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_38.png)

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_40.png)

现在，我们可以使用这个函数来处理数据。

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_42.png)

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_44.png)

```python
# 假设df[‘close’]是我们的价格序列
filtered_data = filter_extreme_MAD(df[‘close‘], n=3)

# 可以绘制图形对比处理前后的数据
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 6))
plt.plot(df[‘close‘].values, label=‘原始数据‘, alpha=0.7)
plt.plot(filtered_data.values, label=‘MAD法处理后‘, alpha=0.9)
plt.legend()
plt.show()
```

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_46.png)

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_48.png)

## 总结

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_50.png)

![](img/a2413cdf66ec11e3fe14768a0afd0cc7_52.png)

本节课中我们一起学习了第二种去极值方法——MAD法。我们了解了它的计算原理，即通过数据的中位数和绝对偏差的中位数来构建一个稳健的数据范围。随后，我们动手实现了对应的Python函数，并演示了如何使用它来处理金融数据序列。MAD法是对百分位法的一种有效补充，在处理具有非正态分布或存在显著异常值的数据时，往往能提供更可靠的结果。