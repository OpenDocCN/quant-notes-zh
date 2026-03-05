# Python金融分析量化交易：P29：2-基于百分位去极值实例 📊

![](img/65901715f75dec69a4189a0f3ea3b211_1.png)

![](img/65901715f75dec69a4189a0f3ea3b211_3.png)

在本节课中，我们将学习一种在金融数据处理中常用的技术——基于百分位法去除数据中的极端值。这种方法的核心思想是，将数据中超出指定百分位范围的值，统一替换为该范围的边界值，从而消除异常值对整体分析的影响。

上一节我们介绍了数据预处理的重要性，本节中我们来看看如何使用具体的百分位法来规范数据范围。

![](img/65901715f75dec69a4189a0f3ea3b211_5.png)

![](img/65901715f75dec69a4189a0f3ea3b211_7.png)

## 核心思路与函数定义

![](img/65901715f75dec69a4189a0f3ea3b211_9.png)

我们的目标是：给定一个数据序列，计算其第一四分位数（Q1，25%分位点）和第三四分位数（Q3，75%分位点）。将所有小于Q1的值替换为Q1，将所有大于Q3的值替换为Q3。这样，数据就被规范到了一个由Q1和Q3定义的范围内。

![](img/65901715f75dec69a4189a0f3ea3b211_11.png)

以下是实现此功能的核心函数：

![](img/65901715f75dec69a4189a0f3ea3b211_13.png)

![](img/65901715f75dec69a4189a0f3ea3b211_15.png)

```python
import pandas as pd
import numpy as np

![](img/65901715f75dec69a4189a0f3ea3b211_17.png)

![](img/65901715f75dec69a4189a0f3ea3b211_19.png)

def filter_extremes_percentile(series, min_percentile=0.25, max_percentile=0.75):
    """
    基于百分位法去除序列中的极端值。
    参数:
        series: 输入的pandas Series数据序列。
        min_percentile: 最小百分位，默认为0.25（Q1）。
        max_percentile: 最大百分位，默认为0.75（Q3）。
    返回:
        处理后的pandas Series序列。
    """
    # 第一步：对数据进行排序
    sorted_series = series.sort_values()
    
    # 第二步：计算指定百分位对应的值
    q = sorted_series.quantile([min_percentile, max_percentile])
    
    # 第三步：使用np.clip函数将数据规范到[Q1, Q3]区间
    filtered_series = np.clip(series, q.iloc[0], q.iloc[1])
    
    return filtered_series
```

![](img/65901715f75dec69a4189a0f3ea3b211_21.png)

## 代码执行步骤详解

![](img/65901715f75dec69a4189a0f3ea3b211_23.png)

接下来，我们详细拆解函数的每一步操作。

### 1. 数据排序
为了准确找到指定百分位对应的数值，首先需要将数据按从小到大的顺序排列。我们使用Pandas的 `sort_values()` 方法。

![](img/65901715f75dec69a4189a0f3ea3b211_25.png)

![](img/65901715f75dec69a4189a0f3ea3b211_27.png)

**公式/代码**：`sorted_series = series.sort_values()`

![](img/65901715f75dec69a4189a0f3ea3b211_29.png)

### 2. 计算百分位值
利用Pandas Series的 `quantile()` 方法，可以方便地计算任意分位点的值。我们传入一个包含最小和最大百分位的列表，即可得到对应的Q1和Q3值。

**公式/代码**：`q = sorted_series.quantile([min_percentile, max_percentile])`
其中，`q.iloc[0]` 对应Q1，`q.iloc[1]` 对应Q3。

![](img/65901715f75dec69a4189a0f3ea3b211_31.png)

### 3. 数据规范化
使用NumPy的 `clip()` 函数，将原始序列中所有小于Q1的值设置为Q1，所有大于Q3的值设置为Q3，介于中间的值则保持不变。

![](img/65901715f75dec69a4189a0f3ea3b211_33.png)

**公式/代码**：`filtered_series = np.clip(series, q.iloc[0], q.iloc[1])`
函数原型为 `np.clip(a, a_min, a_max)`，它将数组 `a` 中的元素限制在 `[a_min, a_max]` 区间内。

## 实际应用与效果对比

定义好函数后，我们可以将其应用于实际的金融数据列。以下是应用过程：

![](img/65901715f75dec69a4189a0f3ea3b211_35.png)

![](img/65901715f75dec69a4189a0f3ea3b211_37.png)

```python
# 假设 `data` 是一个DataFrame，`column_name` 是我们要处理的列名
processed_data = filter_extremes_percentile(data[column_name])
```

![](img/65901715f75dec69a4189a0f3ea3b211_39.png)

为了直观展示去极值的效果，我们可以将处理前后的数据分布进行可视化对比：

![](img/65901715f75dec69a4189a0f3ea3b211_41.png)

```python
import matplotlib.pyplot as plt

![](img/65901715f75dec69a4189a0f3ea3b211_43.png)

# 绘制原始数据分布
plt.figure(figsize=(12, 5))
plt.subplot(1, 2, 1)
data[column_name].plot(kind='hist', title='原始数据分布', color='skyblue')

![](img/65901715f75dec69a4189a0f3ea3b211_45.png)

# 绘制处理后的数据分布
plt.subplot(1, 2, 2)
processed_data.plot(kind='hist', title='去极值后数据分布', color='lightcoral')
plt.tight_layout()
plt.show()
```

![](img/65901715f75dec69a4189a0f3ea3b211_47.png)

![](img/65901715f75dec69a4189a0f3ea3b211_49.png)

从生成的图表中，可以清晰地看到，经过百分位法处理后，数据的尾部极端值被“裁剪”掉了，分布更加集中，有助于后续进行更稳健的统计分析。

![](img/65901715f75dec69a4189a0f3ea3b211_51.png)

## 注意事项与参数调整

![](img/65901715f75dec69a4189a0f3ea3b211_53.png)

![](img/65901715f75dec69a4189a0f3ea3b211_55.png)

在实际的金融因子分析中，直接使用25%和75%作为分位点可能过于严格，会将过多数据视为异常。通常，我们会使用更两端的百分位，例如：

![](img/65901715f75dec69a4189a0f3ea3b211_57.png)

![](img/65901715f75dec69a4189a0f3ea3b211_59.png)

以下是更常用的参数设置：
*   **下限（min_percentile）**：常设置为 `0.025`（2.5%分位）
*   **上限（max_percentile）**：常设置为 `0.975`（97.5%分位）

![](img/65901715f75dec69a4189a0f3ea3b211_61.png)

这样，只剔除分布两端各2.5%的极端值，在去除噪声和保留大部分数据信息之间取得更好的平衡。本节课为了演示效果明显，才选择了较大的范围（25%-75%）。

![](img/65901715f75dec69a4189a0f3ea3b211_63.png)

![](img/65901715f75dec69a4189a0f3ea3b211_65.png)

---

![](img/65901715f75dec69a4189a0f3ea3b211_67.png)

![](img/65901715f75dec69a4189a0f3ea3b211_69.png)

本节课中我们一起学习了基于百分位法去除数据极值的原理与实现。我们掌握了如何通过排序、计算分位数和使用 `np.clip` 函数来规范数据范围。这种方法简单有效，是金融数据预处理中标准化和清洗异常值的常用工具之一。理解并灵活调整百分位参数，对于构建稳健的量化分析模型至关重要。