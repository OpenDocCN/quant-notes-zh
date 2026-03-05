# Python金融量化分析：P29：MAD法去极值演示

![](img/bb18737e3229a13420e04dba4ad3c966_0.png)

![](img/bb18737e3229a13420e04dba4ad3c966_2.png)

在本节课中，我们将学习第二种数据去极值方法——MAD法。我们将详细解释其核心概念、计算步骤，并通过代码演示如何实现它。

## 概述

![](img/bb18737e3229a13420e04dba4ad3c966_4.png)

上一节我们介绍了基于百分位法的去极值方法。本节中，我们来看看另一种稳健的去极值方法：MAD法。MAD是“Median Absolute Deviation”（中位数绝对偏差）的缩写。这种方法通过数据的中位数和偏差来定义正常值的范围，对异常值不敏感，因此在处理包含极端值的数据时非常有效。

## MAD法核心原理

MAD法的核心思想是：先计算数据的中位数，然后计算每个数据点与中位数之差的绝对值，再求这些绝对值的中位数（即MAD值）。最后，以“中位数 ± N倍MAD值”作为正常值的上下界，超出此范围的值被视为极值并进行处理。

![](img/bb18737e3229a13420e04dba4ad3c966_6.png)

![](img/bb18737e3229a13420e04dba4ad3c966_8.png)

以下是计算MAD值并确定边界的公式：

![](img/bb18737e3229a13420e04dba4ad3c966_10.png)

1.  计算原始数据序列的中位数：
    `median = median(series)`
2.  计算每个数据点与中位数之差的绝对值：
    `abs_deviation = |series - median|`
3.  计算这些绝对值的中位数，即MAD值：
    `MAD = median(abs_deviation)`
4.  确定正常值的上下边界：
    `upper_bound = median + N * MAD`
    `lower_bound = median - N * MAD`

![](img/bb18737e3229a13420e04dba4ad3c966_12.png)

![](img/bb18737e3229a13420e04dba4ad3c966_14.png)

其中，`N`是一个常数系数，通常取值为 **1.4826**。这个系数是为了使MAD估计与正态分布的标准差估计保持一致。

![](img/bb18737e3229a13420e04dba4ad3c966_16.png)

## 代码实现

![](img/bb18737e3229a13420e04dba4ad3c966_18.png)

![](img/bb18737e3229a13420e04dba4ad3c966_20.png)

理解了原理后，我们来看看如何用Python代码实现MAD去极值法。我们将按照上述步骤编写一个函数。

![](img/bb18737e3229a13420e04dba4ad3c966_22.png)

![](img/bb18737e3229a13420e04dba4ad3c966_24.png)

![](img/bb18737e3229a13420e04dba4ad3c966_26.png)

以下是实现MAD去极值功能的完整代码：

![](img/bb18737e3229a13420e04dba4ad3c966_28.png)

```python
import pandas as pd
import numpy as np

![](img/bb18737e3229a13420e04dba4ad3c966_30.png)

![](img/bb18737e3229a13420e04dba4ad3c966_32.png)

def filter_extreme_MAD(series, n):
    """
    使用MAD法对序列进行去极值处理。
    参数:
        series: pandas Series, 待处理的数据序列。
        n: float, 系数，通常取1.4826。
    返回:
        series_capped: pandas Series, 处理后的序列。
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)

    # 2. 计算每个数据点与中位数之差的绝对值
    abs_deviation = (series - median).abs()

    # 3. 计算绝对值序列的中位数，即MAD值
    MAD = abs_deviation.quantile(0.5)

    # 4. 计算上下边界
    upper_bound = median + n * MAD
    lower_bound = median - n * MAD

    # 5. 对超出边界的数据进行截断处理
    series_capped = series.clip(lower=lower_bound, upper=upper_bound)

    return series_capped
```

![](img/bb18737e3229a13420e04dba4ad3c966_34.png)

![](img/bb18737e3229a13420e04dba4ad3c966_36.png)

## 函数使用演示

![](img/bb18737e3229a13420e04dba4ad3c966_38.png)

![](img/bb18737e3229a13420e04dba4ad3c966_40.png)

现在，让我们使用这个函数来处理一组示例数据，并观察处理前后的效果。

![](img/bb18737e3229a13420e04dba4ad3c966_42.png)

![](img/bb18737e3229a13420e04dba4ad3c966_44.png)

```python
# 假设我们有一组包含一些极端值的数据
data = pd.Series([1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 100])  # 100是一个明显的极端值

![](img/bb18737e3229a13420e04dba4ad3c966_46.png)

# 应用MAD去极值函数，系数n使用推荐值1.4826
filtered_data = filter_extreme_MAD(data, n=1.4826)

![](img/bb18737e3229a13420e04dba4ad3c966_48.png)

![](img/bb18737e3229a13420e04dba4ad3c966_50.png)

print("原始数据：")
print(data.values)
print("\n经过MAD法处理后的数据：")
print(filtered_data.values)
```

![](img/bb18737e3229a13420e04dba4ad3c966_52.png)

![](img/bb18737e3229a13420e04dba4ad3c966_54.png)

执行上述代码，你会发现原始数据中的极端值`100`被替换为了一个基于中位数和MAD计算出的上限值，从而消除了极值对整体数据分布的影响。

![](img/bb18737e3229a13420e04dba4ad3c966_56.png)

## 总结

![](img/bb18737e3229a13420e04dba4ad3c966_58.png)

![](img/bb18737e3229a13420e04dba4ad3c966_60.png)

本节课中我们一起学习了MAD去极值法。我们首先了解了它的计算原理，即通过数据的中位数和绝对偏差中位数来定义数据的合理范围。然后，我们一步步实现了对应的Python函数，并进行了演示。

![](img/bb18737e3229a13420e04dba4ad3c966_62.png)

![](img/bb18737e3229a13420e04dba4ad3c966_64.png)

与百分位法相比，MAD法不依赖于数据的具体分布，尤其适用于非正态分布或含有大量异常值的数据集，是金融量化分析中一种非常稳健的数据预处理工具。