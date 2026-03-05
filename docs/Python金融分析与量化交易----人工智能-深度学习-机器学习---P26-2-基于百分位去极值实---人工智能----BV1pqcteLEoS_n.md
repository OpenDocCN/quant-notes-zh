# Python金融分析与量化交易实战教程：P26：2-基于百分位去极值实例 📊

![](img/4e1a7838507c36628f810b27f78bdc3a_1.png)

![](img/4e1a7838507c36628f810b27f78bdc3a_3.png)

在本节课中，我们将要学习一种在金融数据分析中常用的数据预处理技术——基于百分位法去除极值。这种方法可以帮助我们将数据规范到一个合理的范围内，减少极端值对后续分析（如因子分析、模型训练）的干扰。

![](img/4e1a7838507c36628f810b27f78bdc3a_5.png)

上一节我们介绍了数据清洗的重要性，本节中我们来看看如何使用具体的分位数（如Q1和Q3）来识别并处理数据中的极值。

![](img/4e1a7838507c36628f810b27f78bdc3a_7.png)

![](img/4e1a7838507c36628f810b27f78bdc3a_9.png)

## 核心思路与函数定义 🎯

![](img/4e1a7838507c36628f810b27f78bdc3a_11.png)

我们的目标是：对于给定的数据列，计算其第一四分位数（Q1，25%）和第三四分位数（Q3，75%）。将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被“修剪”并规范到了Q1到Q3的范围内。

![](img/4e1a7838507c36628f810b27f78bdc3a_13.png)

![](img/4e1a7838507c36628f810b27f78bdc3a_15.png)

以下是实现此功能的核心步骤，我们将它们封装在一个函数中。

![](img/4e1a7838507c36628f810b27f78bdc3a_17.png)

```python
import pandas as pd
import numpy as np

def winsorize_by_percentile(series, low_percentile=0.25, high_percentile=0.75):
    """
    基于百分位法对序列进行去极值处理（缩尾处理）。
    
    参数:
    series (pd.Series): 待处理的数据序列。
    low_percentile (float): 下分位数，默认0.25（Q1）。
    high_percentile (float): 上分位数，默认0.75（Q3）。
    
    返回:
    pd.Series: 处理后的数据序列。
    """
    # 第一步：对数据进行排序，为计算分位数做准备
    sorted_series = series.sort_values()
    
    # 第二步：计算指定分位数的值（即Q1和Q3）
    # 使用pandas的quantile函数可以方便地计算分位数
    q_values = sorted_series.quantile([low_percentile, high_percentile])
    low_bound = q_values.iloc[0]  # 下边界，即Q1的值
    high_bound = q_values.iloc[1] # 上边界，即Q3的值
    
    # 第三步：使用numpy的clip函数将数据规范到[low_bound, high_bound]区间
    # np.clip(array, min, max) 会将array中小于min的值设为min，大于max的值设为max
    winsorized_series = np.clip(series, low_bound, high_bound)
    
    return winsorized_series
```

## 函数应用与效果对比 📈

函数定义完成后，我们可以将其应用于实际的数据列。为了直观展示去极值的效果，我们将同时绘制原始数据和处理后数据的分布图进行对比。

以下是应用函数并可视化的完整流程：

```python
# 假设我们有一个名为`data`的DataFrame，其中包含名为‘column_D’的列
# 1. 应用去极值函数
processed_data = winsorize_by_percentile(data['column_D'], low_percentile=0.25, high_percentile=0.75)

# 2. 可视化对比
import matplotlib.pyplot as plt

![](img/4e1a7838507c36628f810b27f78bdc3a_19.png)

![](img/4e1a7838507c36628f810b27f78bdc3a_21.png)

plt.figure(figsize=(12, 6))

![](img/4e1a7838507c36628f810b27f78bdc3a_23.png)

# 绘制原始数据分布
plt.subplot(2, 1, 1) # 创建2行1列的子图，这是第1个
data['column_D'].plot(kind='hist', bins=50, alpha=0.7, color='blue', edgecolor='black')
plt.title('原始数据分布')
plt.xlabel('数值')
plt.ylabel('频数')

![](img/4e1a7838507c36628f810b27f78bdc3a_25.png)

# 绘制处理后数据分布
plt.subplot(2, 1, 2) # 创建2行1列的子图，这是第2个
processed_data.plot(kind='hist', bins=50, alpha=0.7, color='orange', edgecolor='black')
plt.title('基于百分位去极值后的数据分布')
plt.xlabel('数值')
plt.ylabel('频数')

![](img/4e1a7838507c36628f810b27f78bdc3a_27.png)

![](img/4e1a7838507c36628f810b27f78bdc3a_29.png)

plt.tight_layout() # 自动调整子图参数，使之填充整个图像区域
plt.show()
```

![](img/4e1a7838507c36628f810b27f78bdc3a_31.png)

通过对比上下两个直方图，可以清晰地看到，处理后数据（下图）的左右两侧极端部分被“修剪”掉了，数据分布更加集中，符合我们设定的Q1到Q3的范围。

![](img/4e1a7838507c36628f810b27f78bdc3a_33.png)

## 参数调整与实际应用提示 💡

![](img/4e1a7838507c36628f810b27f78bdc3a_35.png)

![](img/4e1a7838507c36628f810b27f78bdc3a_37.png)

![](img/4e1a7838507c36628f810b27f78bdc3a_39.png)

在实际的金融因子分析中，直接使用25%和75%作为边界可能过于宽松，会将太多正常数据判定为极值。

![](img/4e1a7838507c36628f810b27f78bdc3a_41.png)

![](img/4e1a7838507c36628f810b27f78bdc3a_43.png)

以下是实际应用中常见的参数设置建议：

![](img/4e1a7838507c36628f810b27f78bdc3a_45.png)

![](img/4e1a7838507c36628f810b27f78bdc3a_47.png)

*   **常用范围**：更常见的做法是使用 **`low_percentile=0.025`** 和 **`high_percentile=0.975`**，即只对分布两端各2.5%的极端值进行处理。
*   **灵活调整**：你可以根据具体因子的特性和经验，灵活调整这两个分位数的值。本教程为了在图表上展示更明显的效果，才使用了较宽的范围（25%和75%）。
*   **理解原理**：核心原理是通过分位数确定数据的正常范围边界，然后将超出边界的值拉回到边界上。`np.clip`函数是实现这一步的关键工具。

![](img/4e1a7838507c36628f810b27f78bdc3a_49.png)

![](img/4e1a7838507c36628f810b27f78bdc3a_51.png)

本节课中我们一起学习了基于百分位法进行数据去极值的完整流程。我们首先理解了其核心思想，然后定义了一个可复用的函数，接着将该函数应用于具体数据，并通过可视化对比验证了处理效果。最后，我们讨论了实际应用中如何调整参数以获得更合理的分析结果。掌握这种方法，能有效提升金融数据清洗和预处理的效率与质量。