# Python金融分析与量化交易实战课程：P26：基于百分位去极值实例 📊

![](img/6251662410b2b7b01c04ba89c7b7516d_1.png)

## 概述
在本节课中，我们将学习一种在金融数据分析中常用的数据预处理技术——**基于百分位去极值**。我们将通过一个具体的实例，了解如何识别并处理数据集中的极端值，使数据分布更加稳健，为后续的量化分析打下基础。

![](img/6251662410b2b7b01c04ba89c7b7516d_3.png)

上一节我们介绍了数据清洗的基本概念，本节中我们来看看如何使用分位数来界定并处理异常值。

![](img/6251662410b2b7b01c04ba89c7b7516d_5.png)

![](img/6251662410b2b7b01c04ba89c7b7516d_7.png)

## 核心操作流程
我们的目标是：给定一个数据集，计算其第一四分位数（Q1）和第三四分位数（Q3）。将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被规范到一个由Q1和Q3定义的范围内。

![](img/6251662410b2b7b01c04ba89c7b7516d_9.png)

以下是实现此功能的具体步骤：

![](img/6251662410b2b7b01c04ba89c7b7516d_11.png)

1.  **选择目标列**：首先，我们需要从数据集中指定要进行去极值操作的列。
2.  **编写处理函数**：创建一个函数，该函数接收数据序列以及我们想要保留的最小和最大百分位（例如，25%和75%）。
3.  **计算分位点**：在函数内部，对数据进行排序，并计算指定百分位对应的具体数值（即Q1和Q3）。
4.  **应用范围限制**：使用`numpy`的`clip`函数，将序列中所有小于Q1的值替换为Q1，所有大于Q3的值替换为Q3。
5.  **结果可视化**：最后，我们将绘制处理前后的数据分布图，以直观地对比去极值的效果。

![](img/6251662410b2b7b01c04ba89c7b7516d_13.png)

![](img/6251662410b2b7b01c04ba89c7b7516d_15.png)

## 代码实现与分步解析
接下来，我们通过代码来具体实现上述流程。

![](img/6251662410b2b7b01c04ba89c7b7516d_17.png)

![](img/6251662410b2b7b01c04ba89c7b7516d_19.png)

### 第一步：选择数据列
我们首先需要确定对哪一列数据进行操作。这里为了举例，我们随机选择一列。

```python
# 假设 `data` 是一个Pandas DataFrame
column_name = ‘D‘  # 选择名为‘D‘的列
selected_series = data[column_name]
```

### 第二步：定义去极值函数
我们将创建一个名为 `winsorize_by_quantile` 的函数。它接收三个参数：数据序列 `series`、最小百分位 `low_percent` 和最大百分位 `high_percent`。

```python
import pandas as pd
import numpy as np

def winsorize_by_quantile(series, low_percent, high_percent):
    “““
    基于百分位进行去极值（缩尾）处理。
    参数:
        series: Pandas Series，待处理的数据序列。
        low_percent: float，希望保留的最小百分位（如0.25）。
        high_percent: float，希望保留的最大百分位（如0.75）。
    返回:
        处理后的Pandas Series。
    “““
    # 1. 对数据进行排序
    sorted_series = series.sort_values()
    
    # 2. 计算指定百分位对应的值（Q1和Q3）
    # 使用 quantile 函数计算分位数
    q_values = sorted_series.quantile([low_percent, high_percent])
    low_value = q_values.iloc[0]  # 对应 low_percent 的值
    high_value = q_values.iloc[1] # 对应 high_percent 的值
    
    # 3. 使用 np.clip 将数据限制在 [low_value, high_value] 范围内
    # np.clip(array, min, max) 将数组中小于min的值设为min，大于max的值设为max
    winsorized_series = np.clip(series, low_value, high_value)
    
    return winsorized_series
```

**核心概念解析**：
*   **`series.quantile([q1, q2])`**：这是Pandas Series的方法，用于计算指定分位数的值。传入一个列表`[0.25, 0.75]`，它会返回一个包含Q1和Q3值的Series。
*   **`np.clip(array, a_min, a_max)`**：这是NumPy的函数，用于将数组中的元素限制在`[a_min, a_max]`的区间内。它是实现“小于Q1的等于Q1，大于Q3的等于Q3”这一逻辑的关键。

### 第三步：应用函数并查看结果
现在，我们可以使用定义好的函数来处理之前选定的数据列。

![](img/6251662410b2b7b01c04ba89c7b7516d_21.png)

```python
# 应用去极值函数，这里使用25%和75%作为边界
low_percent = 0.25
high_percent = 0.75

processed_series = winsorize_by_quantile(selected_series, low_percent, high_percent)
```

![](img/6251662410b2b7b01c04ba89c7b7516d_23.png)

![](img/6251662410b2b7b01c04ba89c7b7516d_25.png)

### 第四步：可视化对比
为了更直观地理解处理效果，我们将原始数据和处理后的数据绘制在同一张图上进行对比。

![](img/6251662410b2b7b01c04ba89c7b7516d_27.png)

```python
import matplotlib.pyplot as plt

![](img/6251662410b2b7b01c04ba89c7b7516d_29.png)

# 创建一个图形窗口
plt.figure(figsize=(10, 6))

![](img/6251662410b2b7b01c04ba89c7b7516d_31.png)

# 绘制原始数据（红色虚线）
plt.plot(selected_series.values, label=‘原始数据‘, color=‘red‘, linestyle=‘--‘, alpha=0.7)

![](img/6251662410b2b7b01c04ba89c7b7516d_33.png)

# 绘制处理后的数据（蓝色实线）
plt.plot(processed_series.values, label=‘去极值后数据‘, color=‘blue‘, alpha=0.7)

![](img/6251662410b2b7b01c04ba89c7b7516d_35.png)

# 添加图例和标题
plt.legend()
plt.title(‘基于百分位（25%-75%）去极值效果对比‘)
plt.xlabel(‘样本索引‘)
plt.ylabel(‘数值‘)

![](img/6251662410b2b7b01c04ba89c7b7516d_37.png)

# 显示图形
plt.show()
```

![](img/6251662410b2b7b01c04ba89c7b7516d_39.png)

![](img/6251662410b2b7b01c04ba89c7b7516d_41.png)

**图像解读**：在生成的图表中，红色虚线代表原始数据，其波动范围较大。蓝色实线代表处理后的数据，可以清楚地看到，原先低于Q1和高于Q3的“尖刺”部分被“削平”了，数据被限制在了一个更窄、更稳定的范围内。蓝色线的上下边界即对应我们计算的Q1和Q3值。

![](img/6251662410b2b7b01c04ba89c7b7516d_43.png)

![](img/6251662410b2b7b01c04ba89c7b7516d_45.png)

## 实际应用注意事项
在上面的例子中，我们使用了25%和75%作为分位点，这会将相当大一部分数据视为“极值”并进行调整，这主要是为了演示时效果更明显。

![](img/6251662410b2b7b01c04ba89c7b7516d_47.png)

在真实的金融因子分析或量化策略中，我们通常会使用更严格的边界，例如：
*   **`low_percent = 0.025`** (2.5%)
*   **`high_percent = 0.975`** (97.5%)

![](img/6251662410b2b7b01c04ba89c7b7516d_49.png)

![](img/6251662410b2b7b01c04ba89c7b7516d_51.png)

这样只对分布两端各2.5%的极端值进行处理，能在去除噪音的同时，更好地保留数据的原始信息。你可以通过修改函数调用时的 `low_percent` 和 `high_percent` 参数来灵活调整去极值的严格程度。

![](img/6251662410b2b7b01c04ba89c7b7516d_53.png)

![](img/6251662410b2b7b01c04ba89c7b7516d_55.png)

## 总结
本节课中我们一起学习了**基于百分位去极值**的完整流程。我们掌握了如何通过计算Q1和Q3来定义数据的正常范围，并利用`np.clip`函数将超出此范围的极端值进行修正。这种方法简单有效，是金融数据预处理中标准化、消除异常值影响的常用手段。理解并掌握这一技术，有助于我们获得更高质量、更稳定的数据输入，从而提升后续模型分析和策略回测的可靠性。