# 量化交易教程：P30：MAD法去极值演示

![](img/60884e0c586c172442ac55632c3bf89c_0.png)

![](img/60884e0c586c172442ac55632c3bf89c_2.png)

![](img/60884e0c586c172442ac55632c3bf89c_0.png)

在本节课程中，我们将学习去极值的第二种方法——MAD法。我们将详细介绍其原理、计算步骤，并通过代码演示如何实现。

## 概述

![](img/60884e0c586c172442ac55632c3bf89c_4.png)

上一节我们介绍了百分位法去极值。本节中，我们来看看另一种基于中位数绝对偏差的去极值方法，即MAD法。这种方法通过计算数据的中位数和绝对偏差来确定数据的正常范围，从而识别并处理异常值。

![](img/60884e0c586c172442ac55632c3bf89c_6.png)

## MAD法原理

MAD法的核心思想是利用数据的中位数和“中位数绝对偏差”来设定一个合理的上下界。任何超出此界限的数据点将被视为极值并进行处理。

其计算过程分为两步：
1.  计算原始数据的中位数。
2.  计算每个数据点与中位数之差的绝对值，然后求这些绝对值的中位数，这个值就是“中位数绝对偏差”。

![](img/60884e0c586c172442ac55632c3bf89c_8.png)

得到MAD值后，我们可以设定正常数据的范围。通常，范围的下界是 **中位数 - N × MAD**，上界是 **中位数 + N × MAD**。这里的N是一个常数，通常取值为 **1.4826**。任何落在此范围之外的数据点，将被调整到边界值上。

![](img/60884e0c586c172442ac55632c3bf89c_10.png)

以下是MAD法的核心公式描述：

![](img/60884e0c586c172442ac55632c3bf89c_12.png)

![](img/60884e0c586c172442ac55632c3bf89c_14.png)

1.  计算中位数：
    `median = median(series)`

![](img/60884e0c586c172442ac55632c3bf89c_16.png)

2.  计算中位数绝对偏差：
    `MAD = median(|series - median|)`

![](img/60884e0c586c172442ac55632c3bf89c_18.png)

![](img/60884e0c586c172442ac55632c3bf89c_20.png)

3.  确定上下界：
    `lower_bound = median - N * MAD`
    `upper_bound = median + N * MAD`

![](img/60884e0c586c172442ac55632c3bf89c_22.png)

![](img/60884e0c586c172442ac55632c3bf89c_24.png)

4.  去极值处理：将序列中所有小于`lower_bound`的值替换为`lower_bound`，所有大于`upper_bound`的值替换为`upper_bound`。

![](img/60884e0c586c172442ac55632c3bf89c_26.png)

![](img/60884e0c586c172442ac55632c3bf89c_28.png)

## 代码实现

![](img/60884e0c586c172442ac55632c3bf89c_30.png)

理解了原理后，我们来看看如何用Python代码实现MAD去极值函数。我们将按照上述步骤逐步构建这个函数。

![](img/60884e0c586c172442ac55632c3bf89c_32.png)

![](img/60884e0c586c172442ac55632c3bf89c_34.png)

```python
import pandas as pd
import numpy as np

def filter_extreme_MAD(series, n=1.4826):
    """
    使用MAD法对序列进行去极值处理。
    参数:
        series: 待处理的pandas Series序列。
        n: 用于计算上下界的倍数，默认为1.4826。
    返回:
        处理后的序列。
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)

    # 2. 计算中位数绝对偏差 (MAD)
    # 先计算每个数据点与中位数的绝对差
    abs_diff = (series - median).abs()
    # 再求这些绝对差的中位数
    MAD = abs_diff.quantile(0.5)

    # 3. 计算上下界
    upper_bound = median + n * MAD
    lower_bound = median - n * MAD

    # 4. 进行去极值处理：将超出范围的值截断到边界
    filtered_series = series.clip(lower=lower_bound, upper=upper_bound)

    return filtered_series
```

![](img/60884e0c586c172442ac55632c3bf89c_36.png)

代码说明如下：
*   函数`filter_extreme_MAD`接受一个数据序列`series`和一个倍数参数`n`。
*   使用`series.quantile(0.5)`计算中位数。
*   通过`(series - median).abs()`计算绝对偏差序列，再用`.quantile(0.5)`得到MAD值。
*   根据公式计算上界`upper_bound`和下界`lower_bound`。
*   最后，使用Pandas的`.clip()`方法，将序列中所有小于下界的值设置为下界，所有大于上界的值设置为上界，并返回处理后的序列。

![](img/60884e0c586c172442ac55632c3bf89c_38.png)

![](img/60884e0c586c172442ac55632c3bf89c_40.png)

## 效果演示

![](img/60884e0c586c172442ac55632c3bf89c_42.png)

![](img/60884e0c586c172442ac55632c3bf89c_44.png)

现在，让我们使用这个函数处理一组示例数据，并可视化处理前后的对比。

```python
# 假设我们有一组包含一些潜在异常值的数据
np.random.seed(42)
normal_data = np.random.randn(100) * 10  # 100个正态分布数据
outliers = np.array([50, -45, 60])       # 加入几个明显的异常值
sample_series = pd.Series(np.concatenate([normal_data, outliers]))

![](img/60884e0c586c172442ac55632c3bf89c_46.png)

![](img/60884e0c586c172442ac55632c3bf89c_48.png)

# 应用MAD去极值函数
filtered_series = filter_extreme_MAD(sample_series, n=1.4826)

![](img/60884e0c586c172442ac55632c3bf89c_50.png)

![](img/60884e0c586c172442ac55632c3bf89c_52.png)

# 可视化对比
import matplotlib.pyplot as plt

plt.figure(figsize=(10, 6))
plt.plot(sample_series.values, ‘o‘, label=‘原始数据‘, alpha=0.7)
plt.plot(filtered_series.values, ‘x‘, label=‘MAD去极值后‘, alpha=0.7)
plt.axhline(y=filtered_series.max(), color=‘r‘, linestyle=‘--‘, label=‘上界‘)
plt.axhline(y=filtered_series.min(), color=‘g‘, linestyle=‘--‘, label=‘下界‘)
plt.legend()
plt.title(‘MAD法去极值效果演示‘)
plt.xlabel(‘数据索引‘)
plt.ylabel(‘数值‘)
plt.show()
```

![](img/60884e0c586c172442ac55632c3bf89c_54.png)

![](img/60884e0c586c172442ac55632c3bf89c_56.png)

执行上述代码后，你将看到一张图表。图中：
*   原始数据的异常点（非常高或非常低的点）在去极值后，其值被调整到了红色和绿色虚线所标示的上下界之内。
*   大部分正常数据则保持不变。
*   这直观地展示了MAD法如何有效地将极端值“拉回”到合理的数值范围内。

![](img/60884e0c586c172442ac55632c3bf89c_58.png)

## 总结

![](img/60884e0c586c172442ac55632c3bf89c_60.png)

![](img/60884e0c586c172442ac55632c3bf89c_62.png)

本节课中，我们一起学习了MAD去极值法。我们首先了解了该方法的原理，它通过数据的中位数和绝对偏差中位数来定义数据的正常范围。接着，我们一步步实现了对应的Python函数，并通过示例数据演示了其处理效果。MAD法是对中位数和偏差的稳健估计，对异常值本身不敏感，因此在处理包含异常值的数据时非常有效。掌握这种方法，能为你的金融数据清洗和预处理工具箱增添一个实用的工具。