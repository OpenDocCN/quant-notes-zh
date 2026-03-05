# Python金融分析与量化交易实战课程：P25：基于百分位去极值实例 📊

![](img/58ad435bc011cdb664ca72368f0d0860_1.png)

在本节课中，我们将学习如何使用百分位法（Percentile）对金融数据进行去极值处理。这是一种常见的数据预处理技术，旨在减少极端值（异常值）对后续分析的影响，使数据分布更加稳健。

![](img/58ad435bc011cdb664ca72368f0d0860_3.png)

上一节我们介绍了数据清洗的重要性，本节中我们来看看如何具体实现基于百分位的去极值方法。

![](img/58ad435bc011cdb664ca72368f0d0860_5.png)

## 核心思路与函数定义

![](img/58ad435bc011cdb664ca72368f0d0860_7.png)

![](img/58ad435bc011cdb664ca72368f0d0860_9.png)

我们的目标是：给定一个数据序列，计算其第一四分位数（Q1，25%分位点）和第三四分位数（Q3，75%分位点）。然后，将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被规范到了一个由Q1和Q3定义的范围内。

![](img/58ad435bc011cdb664ca72368f0d0860_11.png)

以下是实现此功能的核心函数：

![](img/58ad435bc011cdb664ca72368f0d0860_13.png)

```python
import pandas as pd
import numpy as np

![](img/58ad435bc011cdb664ca72368f0d0860_15.png)

![](img/58ad435bc011cdb664ca72368f0d0860_17.png)

def percentile_filter(series, min_percentile=0.25, max_percentile=0.75):
    """
    基于百分位法对序列进行去极值处理。
    参数:
        series: pandas Series，待处理的数据列。
        min_percentile: 最小百分位，默认0.25（Q1）。
        max_percentile: 最大百分位，默认0.75（Q3）。
    返回:
        处理后的pandas Series。
    """
    # 1. 对数据进行排序
    sorted_series = series.sort_values()
    
    # 2. 计算指定百分位对应的值
    Q = sorted_series.quantile([min_percentile, max_percentile])
    min_value = Q.iloc[0]  # 对应min_percentile的值
    max_value = Q.iloc[1]  # 对应max_percentile的值
    
    # 3. 使用np.clip将数据规范到[min_value, max_value]区间
    filtered_series = np.clip(series, min_value, max_value)
    
    return filtered_series
```

![](img/58ad435bc011cdb664ca72368f0d0860_19.png)

## 代码执行步骤详解

接下来，我们一步步解析如何使用这个函数。

首先，我们需要指定要对数据框（DataFrame）中的哪一列进行操作。

```python
# 假设我们有一个名为`data`的DataFrame
column_name = ‘D‘  # 选择要处理的列，这里以‘D‘列为例
```

![](img/58ad435bc011cdb664ca72368f0d0860_21.png)

然后，调用我们定义的函数来处理选定的列。

```python
# 应用百分位去极值函数
filtered_data = percentile_filter(data[column_name], min_percentile=0.25, max_percentile=0.75)
```

## 效果可视化对比

![](img/58ad435bc011cdb664ca72368f0d0860_23.png)

为了直观地展示去极值处理的效果，我们可以将原始数据和处理后的数据绘制在同一图表中进行对比。

以下是绘图代码：

![](img/58ad435bc011cdb664ca72368f0d0860_25.png)

```python
import matplotlib.pyplot as plt

![](img/58ad435bc011cdb664ca72368f0d0860_27.png)

![](img/58ad435bc011cdb664ca72368f0d0860_29.png)

# 创建图形，包含两个子图上下排列
fig, axes = plt.subplots(2, 1, figsize=(10, 8))

# 绘制原始数据
axes[0].plot(data[column_name].values, label=‘原始数据‘, color=‘blue‘)
axes[0].set_title(‘原始数据分布‘)
axes[0].legend()
axes[0].grid(True)

![](img/58ad435bc011cdb664ca72368f0d0860_31.png)

# 绘制处理后的数据
axes[1].plot(filtered_data.values, label=‘去极值后数据‘, color=‘orange‘)
axes[1].set_title(‘基于百分位去极值后的数据分布‘)
axes[1].legend()
axes[1].grid(True)

![](img/58ad435bc011cdb664ca72368f0d0860_33.png)

plt.tight_layout()
plt.show()
```

![](img/58ad435bc011cdb664ca72368f0d0860_35.png)

通过对比上下两个图表，可以清晰地看到，经过处理后，数据的上下边界被限制在了计算出的Q1和Q3值之内。原先低于Q1的极低值被提升至Q1，高于Q3的极高值被降低至Q3，从而使数据的波动范围受到控制。

![](img/58ad435bc011cdb664ca72368f0d0860_37.png)

## 关于参数选择的说明

![](img/58ad435bc011cdb664ca72368f0d0860_39.png)

在实例中，我们使用了25%和75%作为分位点，这会将相当一部分数据视为“极值”并进行调整。在实际的金融因子分析中，这个范围通常会更严格。

![](img/58ad435bc011cdb664ca72368f0d0860_41.png)

![](img/58ad435bc011cdb664ca72368f0d0860_43.png)

以下是实际应用中更常见的参数设置：

![](img/58ad435bc011cdb664ca72368f0d0860_45.png)

![](img/58ad435bc011cdb664ca72368f0d0860_47.png)

- **更常用的范围**：例如使用2.5%和97.5%作为分位点（`min_percentile=0.025`, `max_percentile=0.975`）。这样只对分布两端各2.5%的极端值进行处理，保留了更多原始数据的信息。
- **选择依据**：分位点的选择取决于具体因子特性和研究目的。需要对因子的历史分布进行分析后确定。

![](img/58ad435bc011cdb664ca72368f0d0860_49.png)

您可以在调用函数时轻松修改这些参数：

![](img/58ad435bc011cdb664ca72368f0d0860_51.png)

![](img/58ad435bc011cdb664ca72368f0d0860_53.png)

```python
# 使用更严格（范围更小）的百分位，如2.5%和97.5%
filtered_data_strict = percentile_filter(data[column_name], min_percentile=0.025, max_percentile=0.975)
```

![](img/58ad435bc011cdb664ca72368f0d0860_55.png)

![](img/58ad435bc011cdb664ca72368f0d0860_57.png)

本节课中我们一起学习了基于百分位进行去极值处理的完整流程。我们首先理解了其核心思想——利用分位数界定数据的“正常”范围，并将超出此范围的极值拉回边界。接着，我们定义了一个可复用的函数，并逐步演示了如何对指定数据列应用此函数。最后，通过可视化对比，我们直观地看到了处理前后数据分布的变化。掌握这种方法，能有效提升金融数据在量化建模中的稳健性和可靠性。在实际操作中，请根据因子的具体特性调整百分位参数，以达到最佳的数据清洗效果。