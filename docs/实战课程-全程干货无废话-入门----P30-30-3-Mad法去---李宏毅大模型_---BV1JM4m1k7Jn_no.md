# Python金融分析与量化交易实战：P30：30.3-MAD法去极值演示 📊

![](img/bc5aba2ab34762feffb4e97c3227da7f_0.png)

![](img/bc5aba2ab34762feffb4e97c3227da7f_2.png)

在本节课程中，我们将学习第二种数据去极值方法——**MAD法**。我们将详细解释其核心概念、计算步骤，并通过代码演示如何实现它。

## 概述

![](img/bc5aba2ab34762feffb4e97c3227da7f_4.png)

上一节我们介绍了基于百分位数的去极值方法。本节中，我们来看看另一种稳健的去极值方法：**MAD法**。MAD是“Median Absolute Deviation”（中位数绝对偏差）的缩写。这种方法通过数据的中位数和偏差来定义正常值的范围，对异常值不敏感，因此在金融数据分析中非常实用。

## MAD法核心原理

MAD法的核心思想是：先计算数据序列的中位数，然后计算每个数据点与中位数之差的绝对值，再求这些绝对值的中位数（即MAD值）。最后，根据中位数和MAD值设定一个合理的范围，超出此范围的值被视为极值并进行处理。

![](img/bc5aba2ab34762feffb4e97c3227da7f_6.png)

![](img/bc5aba2ab34762feffb4e97c3227da7f_8.png)

以下是计算上下界限的**公式**：
*   **中位数**：`median = 序列.quantile(0.5)`
*   **MAD值**：`mad = (|序列 - median|).median()`
*   **上界**：`upper_bound = median + n * mad`
*   **下界**：`lower_bound = median - n * mad`

![](img/bc5aba2ab34762feffb4e97c3227da7f_10.png)

![](img/bc5aba2ab34762feffb4e97c3227da7f_12.png)

其中，`n`是一个常数系数，通常取值为 **1.4826**。设定界限后，处理逻辑与百分位法类似：将大于上界或小于下界的数据替换为相应的边界值。

## 代码实现

![](img/bc5aba2ab34762feffb4e97c3227da7f_14.png)

![](img/bc5aba2ab34762feffb4e97c3227da7f_16.png)

![](img/bc5aba2ab34762feffb4e97c3227da7f_18.png)

理解了原理后，我们通过代码来实现MAD去极值函数。

![](img/bc5aba2ab34762feffb4e97c3227da7f_20.png)

![](img/bc5aba2ab34762feffb4e97c3227da7f_22.png)

以下是MAD去极值函数的**代码**实现：

![](img/bc5aba2ab34762feffb4e97c3227da7f_24.png)

![](img/bc5aba2ab34762feffb4e97c3227da7f_26.png)

```python
def filter_extreme_mad(series, n=1.4826):
    """
    使用MAD法对序列进行去极值处理。
    参数:
        series: pandas Series, 待处理的数据序列。
        n: float, 计算上下界时使用的系数，默认为1.4826。
    返回:
        pandas Series, 处理后的数据序列。
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)
    
    # 2. 计算每个数据点与中位数之差的绝对值，再求其中位数，得到MAD值
    mad = (series - median).abs().median()
    
    # 3. 利用中位数、MAD值和系数n计算上下界
    upper_bound = median + n * mad
    lower_bound = median - n * mad
    
    # 4. 将超出上下界的数据替换为边界值
    filtered_series = series.copy()
    filtered_series[filtered_series > upper_bound] = upper_bound
    filtered_series[filtered_series < lower_bound] = lower_bound
    
    return filtered_series
```

![](img/bc5aba2ab34762feffb4e97c3227da7f_28.png)

## 函数应用与结果可视化

![](img/bc5aba2ab34762feffb4e97c3227da7f_30.png)

![](img/bc5aba2ab34762feffb4e97c3227da7f_32.png)

定义好函数后，我们可以将其应用于实际数据，并观察处理效果。

![](img/bc5aba2ab34762feffb4e97c3227da7f_34.png)

![](img/bc5aba2ab34762feffb4e97c3227da7f_36.png)

以下是应用函数并绘制对比图的**代码**：

![](img/bc5aba2ab34762feffb4e97c3227da7f_38.png)

```python
# 假设 `data_series` 是您的原始数据序列
filtered_series_mad = filter_extreme_mad(data_series, n=1.4826)

![](img/bc5aba2ab34762feffb4e97c3227da7f_40.png)

![](img/bc5aba2ab34762feffb4e97c3227da7f_42.png)

![](img/bc5aba2ab34762feffb4e97c3227da7f_44.png)

# 可视化对比原始数据与MAD法处理后的数据
import matplotlib.pyplot as plt

![](img/bc5aba2ab34762feffb4e97c3227da7f_46.png)

plt.figure(figsize=(10, 6))
plt.plot(data_series.values, label='原始数据', alpha=0.7)
plt.plot(filtered_series_mad.values, label='MAD法处理后数据', alpha=0.9)
plt.legend()
plt.title('MAD法去极值效果对比')
plt.xlabel('索引')
plt.ylabel('数值')
plt.show()
```

![](img/bc5aba2ab34762feffb4e97c3227da7f_48.png)

![](img/bc5aba2ab34762feffb4e97c3227da7f_50.png)

运行以上代码，您将看到一张图表。图中，原始数据的异常尖峰（极值）被“削平”至蓝色线条所代表的合理范围内，而大部分正常数据则保持不变。这就是MAD法去极值的效果。

![](img/bc5aba2ab34762feffb4e97c3227da7f_52.png)

![](img/bc5aba2ab34762feffb4e97c3227da7f_54.png)

## 总结

![](img/bc5aba2ab34762feffb4e97c3227da7f_56.png)

![](img/bc5aba2ab34762feffb4e97c3227da7f_58.png)

本节课中我们一起学习了**MAD去极值法**。我们首先了解了其基于中位数和绝对偏差的稳健计算原理，然后逐步实现了核心的计算步骤，并最终编写了一个完整的处理函数。通过代码演示和结果可视化，我们直观地看到了MAD法如何有效地识别并处理数据中的极端值，为后续的金融数据分析提供了更干净、更可靠的数据基础。