# Python金融量化分析：P29：基于百分位去极值实例 📊

![](img/ac3818d1a9561985bace0cdf4f261f66_1.png)

![](img/ac3818d1a9561985bace0cdf4f261f66_3.png)

## 概述
在本节课中，我们将学习一种在金融数据分析中常用的数据预处理技术：**基于百分位去极值**。我们将通过一个具体的Python实例，学习如何识别并处理数据集中的极端值，从而让数据更加规范，便于后续的分析和建模。

![](img/ac3818d1a9561985bace0cdf4f261f66_5.png)

上一节我们介绍了数据清洗的重要性，本节中我们来看看如何使用分位数（如Q1和Q3）来界定数据的正常范围，并将超出此范围的极端值“拉回”到边界上。

![](img/ac3818d1a9561985bace0cdf4f261f66_7.png)

![](img/ac3818d1a9561985bace0cdf4f261f66_9.png)

## 核心步骤与代码实现

![](img/ac3818d1a9561985bace0cdf4f261f66_11.png)

![](img/ac3818d1a9561985bace0cdf4f261f66_13.png)

### 1. 明确目标与思路
我们的目标是：对于选定的一列数据，计算其第一四分位数（Q1，25%）和第三四分位数（Q3，75%）。然后，将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被规范到了一个由Q1和Q3确定的范围内。

![](img/ac3818d1a9561985bace0cdf4f261f66_15.png)

![](img/ac3818d1a9561985bace0cdf4f261f66_17.png)

以下是实现此功能的核心步骤：

![](img/ac3818d1a9561985bace0cdf4f261f66_19.png)

### 2. 编写去极值函数
首先，我们需要编写一个函数来完成上述操作。这个函数将接收一个Pandas Series数据、一个最小百分位参数和一个最大百分位参数。

![](img/ac3818d1a9561985bace0cdf4f261f66_21.png)

![](img/ac3818d1a9561985bace0cdf4f261f66_23.png)

```python
import pandas as pd
import numpy as np

def winsorize_by_percentile(series, low_percentile=0.25, high_percentile=0.75):
    """
    基于百分位进行去极值处理（缩尾处理）。
    参数:
        series: 待处理的数据列，Pandas Series类型。
        low_percentile: 下界百分位，默认0.25（Q1）。
        high_percentile: 上界百分位，默认0.75（Q3）。
    返回:
        处理后的数据列。
    """
    # 步骤1：对数据进行排序
    sorted_series = series.sort_values()
    
    # 步骤2：计算指定百分位对应的实际数值
    # 使用quantile函数计算分位数
    q = sorted_series.quantile([low_percentile, high_percentile])
    low_bound = q.iloc[0]  # 下边界值（如Q1）
    high_bound = q.iloc[1] # 上边界值（如Q3）
    
    # 步骤3：使用np.clip函数将数据规范到[low_bound, high_bound]区间内
    # 小于low_bound的值变为low_bound，大于high_bound的值变为high_bound
    winsorized_series = np.clip(series, low_bound, high_bound)
    
    return winsorized_series
```

![](img/ac3818d1a9561985bace0cdf4f261f66_25.png)

![](img/ac3818d1a9561985bace0cdf4f261f66_27.png)

**代码解析：**
*   `series.sort_values()`: 将数据从小到大排序，这是计算准确分位数的前提。
*   `series.quantile([low_percentile, high_percentile])`: Pandas内置函数，直接计算指定百分位对应的数值。它返回一个包含两个值的Series。
*   `np.clip(series, a_min, a_max)`: NumPy的核心函数，用于将数组中的所有元素限制在区间 `[a_min, a_max]` 内。这正是我们需要的“小于下限的等于下限，大于上限的等于上限”的操作。

![](img/ac3818d1a9561985bace0cdf4f261f66_29.png)

### 3. 应用函数并可视化结果
函数编写完成后，我们可以将其应用到具体的金融数据列上，并通过绘图对比处理前后的差异。

![](img/ac3818d1a9561985bace0cdf4f261f66_31.png)

```python
# 假设 `data` 是一个包含金融数据的DataFrame，例如股票因子数据
# 选择需要处理的一列，这里以名为‘factor_column’的列为例
column_to_process = ‘factor_column‘

# 应用去极值函数
# 这里使用默认的25%和75%作为边界，实际应用中可根据需要调整，例如使用(0.025, 0.975)
processed_series = winsorize_by_percentile(data[column_to_process])

![](img/ac3818d1a9561985bace0cdf4f261f66_33.png)

# 可视化对比
import matplotlib.pyplot as plt

![](img/ac3818d1a9561985bace0cdf4f261f66_35.png)

plt.figure(figsize=(12, 6))

![](img/ac3818d1a9561985bace0cdf4f261f66_37.png)

![](img/ac3818d1a9561985bace0cdf4f261f66_39.png)

# 绘制原始数据分布
plt.subplot(2, 1, 1) # 创建2行1列的子图，当前为第1个
plt.plot(data[column_to_process].values, ‘b.‘, alpha=0.6, label=‘Original Data‘)
plt.title(‘Original Data Distribution‘)
plt.legend()

![](img/ac3818d1a9561985bace0cdf4f261f66_41.png)

# 绘制去极值后的数据分布
plt.subplot(2, 1, 2) # 当前为第2个子图
plt.plot(processed_series.values, ‘r.‘, alpha=0.6, label=‘Winsorized Data‘)
plt.title(‘Data Distribution After Winsorizing‘)
plt.legend()

![](img/ac3818d1a9561985bace0cdf4f261f66_43.png)

plt.tight_layout() # 自动调整子图参数，使之填充整个图像区域
plt.show()
```

![](img/ac3818d1a9561985bace0cdf4f261f66_45.png)

**可视化说明：**
通过上下两个子图的对比，可以清晰地看到处理效果。上图是原始数据，可能存在一些远离核心区域的散点（极端值）。下图是处理后的数据，所有数据点都被限制在了一个更紧凑的纵向范围内，两端的极端值被“拉回”到了边界线附近。

![](img/ac3818d1a9561985bace0cdf4f261f66_47.png)

![](img/ac3818d1a9561985bace0cdf4f261f66_49.png)

### 4. 实际应用注意事项
在真实的金融因子分析中，直接使用25%和75%作为边界通常过于宽松，可能会将太多正常数据误判为极端值。

![](img/ac3818d1a9561985bace0cdf4f261f66_51.png)

以下是更常用的参数设置建议：

![](img/ac3818d1a9561985bace0cdf4f261f66_53.png)

![](img/ac3818d1a9561985bace0cdf4f261f66_55.png)

![](img/ac3818d1a9561985bace0cdf4f261f66_57.png)

*   **更严格的边界**：通常会选择两端各2.5%或5%的数据作为极端值处理。例如，设置 `low_percentile=0.025` 和 `high_percentile=0.975`。
*   **因子特异性**：不同因子的数据分布特征不同，最佳的百分位阈值可能需要通过回测和经验来确定。
*   **方法灵活性**：我们编写的函数允许你传入任意百分位参数，方便你根据不同的数据特征和需求进行灵活调整。

![](img/ac3818d1a9561985bace0cdf4f261f66_59.png)

![](img/ac3818d1a9561985bace0cdf4f261f66_61.png)

## 总结
本节课我们一起学习了基于百分位进行去极值（缩尾处理）的实战方法。

![](img/ac3818d1a9561985bace0cdf4f261f66_63.png)

![](img/ac3818d1a9561985bace0cdf4f261f66_65.png)

我们首先明确了处理逻辑：**利用分位数确定数据的合理上下限，并将超出限值的数据替换为边界值**。接着，我们通过三个关键步骤实现了它：**数据排序**、**计算分位数值**、**使用`np.clip`函数进行截断**。最后，我们通过可视化对比验证了处理效果，并讨论了在实际金融量化分析中如何选择更合理的百分位参数。

![](img/ac3818d1a9561985bace0cdf4f261f66_67.png)

![](img/ac3818d1a9561985bace0cdf4f261f66_69.png)

这种方法能有效平滑极端值对整体数据分析（如计算均值、方差、构建模型）造成的干扰，是金融数据预处理中一项基础且重要的技能。