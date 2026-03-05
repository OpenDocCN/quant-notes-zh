# Python金融分析+量化交易：P29：基于百分位去极值实例 📊

![](img/68389894928f8936e61c838b2b4be50e_1.png)

在本节课中，我们将学习一种在金融数据分析中常用的数据预处理技术——基于百分位法去除极值。我们将通过一个具体的实例，理解其底层原理，并编写代码实现该功能。

![](img/68389894928f8936e61c838b2b4be50e_3.png)

## 概述

![](img/68389894928f8936e61c838b2b4be50e_5.png)

在金融数据分析中，原始数据常常包含一些极端值（或称“离群点”），这些值可能由数据错误或市场异常波动导致，会干扰我们对数据整体分布和趋势的判断。基于百分位去极值法是一种简单有效的方法，其核心思想是：将数据限制在一个由特定百分位数（如25%分位数和75%分位数）定义的合理范围内，超出此范围的值将被“截断”或“缩尾”至边界值。

![](img/68389894928f8936e61c838b2b4be50e_7.png)

上一节我们介绍了数据预处理的重要性，本节中我们来看看如何具体实现基于百分位的去极值操作。

![](img/68389894928f8936e61c838b2b4be50e_9.png)

## 实现步骤详解

![](img/68389894928f8936e61c838b2b4be50e_11.png)

以下是实现基于百分位去极值功能的具体步骤。

![](img/68389894928f8936e61c838b2b4be50e_13.png)

![](img/68389894928f8936e61c838b2b4be50e_15.png)

### 1. 定义处理函数

![](img/68389894928f8936e61c838b2b4be50e_17.png)

首先，我们需要定义一个函数，该函数接收一个数据序列（Pandas Series）以及我们希望保留的最小和最大百分位（如下四分位数Q1和上四分位数Q3），然后返回处理后的序列。

![](img/68389894928f8936e61c838b2b4be50e_19.png)

```python
import pandas as pd
import numpy as np

![](img/68389894928f8936e61c838b2b4be50e_21.png)

def winsorize_by_percentile(series, low_percentile, high_percentile):
    """
    基于百分位对序列进行去极值（缩尾）处理。
    参数:
        series: Pandas Series，待处理的数据列。
        low_percentile: 最小百分位，如0.25。
        high_percentile: 最大百分位，如0.75。
    返回:
        处理后的Pandas Series。
    """
```

### 2. 对数据进行排序

![](img/68389894928f8936e61c838b2b4be50e_23.png)

为了计算指定百分位对应的具体数值，我们需要先将数据按从小到大的顺序排列。

![](img/68389894928f8936e61c838b2b4be50e_25.png)

```python
    # 对数据进行排序
    sorted_series = series.sort_values()
```

![](img/68389894928f8936e61c838b2b4be50e_27.png)

### 3. 计算边界值

![](img/68389894928f8936e61c838b2b4be50e_29.png)

使用Pandas的`quantile`函数，我们可以方便地计算出排序后序列在指定百分位处的值。这些值将作为我们“截断”数据的上下边界。

```python
    # 计算指定百分位对应的边界值
    bounds = sorted_series.quantile([low_percentile, high_percentile])
    lower_bound = bounds.iloc[0]  # 下边界，如Q1
    upper_bound = bounds.iloc[1]  # 上边界，如Q3
```

![](img/68389894928f8936e61c838b2b4be50e_31.png)

### 4. 应用边界进行数据规整

![](img/68389894928f8936e61c838b2b4be50e_33.png)

最后，我们使用NumPy的`clip`函数，将原始序列中所有小于下边界的值设置为下边界，将所有大于上边界的值设置为上边界，从而完成去极值操作。

```python
    # 使用clip函数将数据限制在[lower_bound, upper_bound]范围内
    winsorized_series = np.clip(series, lower_bound, upper_bound)
    return winsorized_series
```

![](img/68389894928f8936e61c838b2b4be50e_35.png)

## 完整代码与效果演示

![](img/68389894928f8936e61c838b2b4be50e_37.png)

现在，让我们将上述步骤整合，并应用于一个示例数据集，以观察处理效果。

![](img/68389894928f8936e61c838b2b4be50e_39.png)

```python
# 假设`data`是一个包含多列金融数据的DataFrame，我们选择其中一列进行处理
column_name = ‘某因子列‘  # 请替换为实际的列名
original_series = data[column_name]

![](img/68389894928f8936e61c838b2b4be50e_41.png)

# 调用函数进行去极值处理，这里使用0.25和0.75作为示例边界
winsorized_series = winsorize_by_percentile(original_series, 0.25, 0.75)

![](img/68389894928f8936e61c838b2b4be50e_43.png)

# 可视化对比原始数据与处理后的数据
import matplotlib.pyplot as plt

![](img/68389894928f8936e61c838b2b4be50e_45.png)

plt.figure(figsize=(12, 6))

![](img/68389894928f8936e61c838b2b4be50e_47.png)

# 绘制原始数据分布
plt.subplot(2, 1, 1)
plt.hist(original_series, bins=50, alpha=0.7, color=‘blue‘, label=‘原始数据‘)
plt.title(‘原始数据分布‘)
plt.legend()

![](img/68389894928f8936e61c838b2b4be50e_49.png)

# 绘制去极值后的数据分布
plt.subplot(2, 1, 2)
plt.hist(winsorized_series, bins=50, alpha=0.7, color=‘orange‘, label=‘去极值后数据‘)
plt.title(‘基于百分位去极值后数据分布‘)
plt.legend()

![](img/68389894928f8936e61c838b2b4be50e_51.png)

plt.tight_layout()
plt.show()
```

![](img/68389894928f8936e61c838b2b4be50e_53.png)

![](img/68389894928f8936e61c838b2b4be50e_55.png)

通过对比上下两个分布图，可以清晰地看到，经过处理后，数据的尾部极端值被“截断”并集中到了我们设定的边界（Q1和Q3）附近，数据的整体分布范围被规范到了一个更合理的区间内。

![](img/68389894928f8936e61c838b2b4be50e_57.png)

![](img/68389894928f8936e61c838b2b4be50e_59.png)

## 总结

![](img/68389894928f8936e61c838b2b4be50e_61.png)

本节课中我们一起学习了基于百分位去极值的原理与实现。我们掌握了如何通过定义处理函数、排序数据、计算百分位边界以及应用`np.clip`函数来规整数据。这种方法的核心公式可概括为：

![](img/68389894928f8936e61c838b2b4be50e_63.png)

**处理后的值 = clip(原始值， Q1, Q3)**

![](img/68389894928f8936e61c838b2b4be50e_65.png)

![](img/68389894928f8936e61c838b2b4be50e_67.png)

其中，小于Q1的值被提升至Q1，大于Q3的值被降低至Q3。在实际金融因子分析中，通常会使用更严格的百分位（如2.5%和97.5%）来定义异常值边界。理解并掌握这一基础的数据清洗技术，对于后续构建稳健的量化交易模型至关重要。