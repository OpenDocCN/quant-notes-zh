# Python金融分析与量化交易实战教程：P26：2-基于百分位去极值实例 📊

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_1.png)

在本节课中，我们将学习如何使用基于百分位的方法来处理数据中的极端值。这种方法在金融数据分析中非常实用，可以帮助我们平滑数据，减少异常值对分析结果的影响。

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_3.png)

上一节我们介绍了数据预处理的重要性，本节中我们来看看如何具体实现基于百分位的去极值操作。

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_5.png)

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_7.png)

## 核心思路 💡

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_9.png)

我们的目标是：给定一组数据，计算出其第一四分位数（Q1，25%分位点）和第三四分位数（Q3，75%分位点）。然后，将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被规范到了一个由Q1和Q3定义的范围内。

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_11.png)

## 实现步骤 🛠️

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_13.png)

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_15.png)

以下是实现基于百分位去极值功能的具体步骤。

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_17.png)

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_19.png)

### 1. 选择目标数据列

首先，我们需要指定要对数据框中的哪一列执行操作。这里我们以列 `‘D’` 为例。

```python
column_name = 'D'
```

### 2. 定义去极值函数

接下来，我们编写一个函数来完成核心的去极值逻辑。这个函数接收一个Pandas Series数据、一个最小百分位参数和一个最大百分位参数。

```python
import pandas as pd
import numpy as np

def winsorize_by_percentile(series, low_percentile=0.25, high_percentile=0.75):
    """
    基于百分位进行去极值处理（缩尾处理）。
    参数:
        series: 待处理的数据序列 (Pandas Series)。
        low_percentile: 下限百分位，默认0.25（Q1）。
        high_percentile: 上限百分位，默认0.75（Q3）。
    返回:
        处理后的数据序列。
    """
    # 对数据进行排序，为计算分位数做准备
    sorted_series = series.sort_values()

    # 计算指定百分位对应的值
    # quantile函数可以一次性计算多个分位点
    percentiles = sorted_series.quantile([low_percentile, high_percentile])

    # 获取下限值（Q1）和上限值（Q3）
    low_bound = percentiles.iloc[0]  # 对应low_percentile的值
    high_bound = percentiles.iloc[1] # 对应high_percentile的值

    # 使用np.clip函数将数据限制在[low_bound, high_bound]范围内
    # 小于low_bound的值变为low_bound，大于high_bound的值变为high_bound
    winsorized_series = np.clip(series, low_bound, high_bound)

    return winsorized_series
```

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_21.png)

**函数解析：**
*   `series.sort_values()`: 对序列进行排序，这是准确计算分位数的前提。
*   `series.quantile([p1, p2])`: Pandas内置函数，用于计算序列在指定分位点 `p1`, `p2` 的值。返回一个包含这些值的Series。
*   `np.clip(array, min, max)`: NumPy函数，将数组中的所有元素限制在 `[min, max]` 区间内。它是实现“小于下限的设为下限，大于上限的设为上限”这一操作的简洁方法。

### 3. 应用函数并可视化结果

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_23.png)

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_25.png)

定义好函数后，我们可以将其应用到选定的数据列上，并通过绘图对比处理前后的数据分布。

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_27.png)

```python
import matplotlib.pyplot as plt

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_29.png)

# 假设 `data` 是一个包含金融数据的Pandas DataFrame
# 应用去极值函数
winsorized_data = winsorize_by_percentile(data[column_name])

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_31.png)

# 创建图形，对比原始数据与处理后的数据
plt.figure(figsize=(12, 6))

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_33.png)

# 绘制原始数据
plt.subplot(2, 1, 1) # 2行1列，第1个图
data[column_name].plot(title=f‘Original Data - Column {column_name}’)
plt.grid(True)

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_35.png)

# 绘制去极值后的数据
plt.subplot(2, 1, 2) # 2行1列，第2个图
winsorized_data.plot(title=f‘Winsorized Data (Percentile Method) - Column {column_name}’, color=‘orange’)
plt.grid(True)

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_37.png)

plt.tight_layout() # 自动调整子图参数，使之填充整个图像区域
plt.show()
```

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_39.png)

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_41.png)

通过上下排列的图表，可以清晰地看到，经过处理后的数据（下图）其极端的高值和低值已经被“截断”，数据分布更加集中，而中间部分的数据形态得以保留。

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_43.png)

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_45.png)

## 注意事项与扩展 📝

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_47.png)

在实际的金融因子分析中，直接使用25%和75%作为分位点可能过于宽松，会将太多正常数据视为异常。通常，我们会使用更极端的百分位，例如 **2.5%** 和 **97.5%**，或者 **1%** 和 **99%**。这样可以只对真正的极端异常值进行处理。

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_49.png)

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_51.png)

```python
# 更常用的参数设置：去除头尾各2.5%的极端值
winsorized_data_strict = winsorize_by_percentile(data[column_name], low_percentile=0.025, high_percentile=0.975)
```

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_53.png)

![](img/e7e4aa5e7334b684b0b2b4b0a62d3032_55.png)

本节课中我们一起学习了如何基于百分位法对数据进行去极值处理。我们掌握了其核心思想，即通过分位数确定数据的正常范围，并将超出此范围的值替换为边界值。我们使用Pandas的 `quantile` 函数和NumPy的 `clip` 函数高效地实现了这一过程，并通过可视化对比验证了处理效果。这是数据预处理中清洗异常值的一种经典且有效的方法。