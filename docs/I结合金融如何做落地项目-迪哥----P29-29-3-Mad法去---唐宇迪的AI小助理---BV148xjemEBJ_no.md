# Python金融分析与量化交易实战：P29：29.3-MAD法去极值演示 📊

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_0.png)

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_2.png)

在本节课中，我们将要学习第二种数据去极值的方法——MAD法。我们将详细解释其原理，并通过Python代码演示如何实现。

## 概述

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_4.png)

上一节我们介绍了百分位法去极值，本节中我们来看看另一种稳健的方法——MAD法。MAD法通过计算数据的中位数绝对偏差来设定合理的上下界，从而识别并处理异常值。

## MAD法原理详解

MAD法的核心思想是使用数据的中位数和“中位数绝对偏差”来定义一个正常数据的范围。其计算过程分为两步。

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_6.png)

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_8.png)

首先，我们需要计算一个名为“中位数绝对偏差”的值。给定一个数据序列，我们先计算该序列的中位数。然后，计算序列中每个数据点与该中位数的绝对差值，从而得到一个新的差值序列。最后，对这个差值序列再次求中位数，得到的结果就是MAD值。

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_10.png)

用公式可以描述为：
**MAD = median( |X_i - median(X)| )**
其中，`X`代表原始数据序列，`median`表示求中位数操作。

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_12.png)

接下来，我们利用计算出的MAD值来设定数据的正常范围。通常，我们会设定一个系数N（一个常数），上下界通过以下公式确定：
**上界 = median(X) + N * MAD**
**下界 = median(X) - N * MAD**
系数N通常取经验值 **1.4826**。任何超出这个上下界范围的数据点，都将被视为极值并进行处理（例如截断或替换）。

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_14.png)

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_16.png)

## 代码实现

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_18.png)

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_20.png)

以下是使用Python和Pandas库实现MAD去极值法的具体步骤。我们将按照上述原理，逐步编写一个函数。

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_22.png)

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_24.png)

首先，我们定义一个名为 `filter_extreme_MAD` 的函数。它接收一个Pandas Series类型的数据序列 `series` 和一个系数 `n` 作为参数。

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_26.png)

```python
def filter_extreme_MAD(series, n):
    # 第一步：计算原始序列的中位数
    median = series.quantile(0.5)
    
    # 第二步：计算中位数绝对偏差 (MAD)
    # 计算每个数据点与中位数的绝对差值
    abs_diff = (series - median).abs()
    # 对差值序列求中位数，得到MAD值
    mad = abs_diff.quantile(0.5)
    
    # 第三步：利用MAD值计算上下界
    high = median + n * mad
    low = median - n * mad
    
    # 第四步：对超出界限的数据进行截断处理
    # 将大于上界的值设为上界，小于下界的值设为下界
    filtered_series = series.clip(lower=low, upper=high)
    
    return filtered_series
```

代码编写完成后，我们可以使用这个函数来处理数据。

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_28.png)

```python
# 假设 `data_series` 是包含待处理数据的Pandas Series
# 使用通常的系数 1.4826 调用函数
filtered_data = filter_extreme_MAD(data_series, n=1.4826)

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_30.png)

# 可以绘制图表对比处理前后的数据分布
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 5))
plt.subplot(1, 2, 1)
data_series.plot(title=‘原始数据’)
plt.subplot(1, 2, 2)
filtered_data.plot(title=‘MAD法去极值后数据’)
plt.show()
```

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_32.png)

执行以上代码后，大于上界的蓝色部分数据和小于下界的蓝色部分数据都会被调整到边界值，从而完成去极值操作。

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_34.png)

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_36.png)

## 总结

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_38.png)

![](img/a847a2dd39849be2ef39d3d1b0d65c5d_40.png)

本节课中我们一起学习了MAD去极值法。我们首先理解了MAD法的核心原理，即通过数据的中位数和其绝对偏差来定义数据的合理范围。然后，我们一步步实现了对应的Python函数，并演示了如何使用它来处理实际数据。与百分位法相比，MAD法对极端值不敏感，因此在数据存在异常值时更为稳健。掌握这种方法能为金融数据的清洗和预处理提供又一个有效的工具。