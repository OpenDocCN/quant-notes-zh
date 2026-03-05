# Python金融量化与股票交易：P29：基于百分位去极值实例 📊

![](img/c96f3a91dea1cd31c3383e2470fa9609_1.png)

在本节课中，我们将学习如何使用百分位法（Percentile）对金融数据进行去极值处理。这是一种常见的数据预处理技术，旨在将数据规范到一个合理的范围内，减少极端值对后续分析的影响。

![](img/c96f3a91dea1cd31c3383e2470fa9609_3.png)

上一节我们介绍了数据预处理的重要性，本节中我们来看看如何具体实现基于百分位的去极值方法。

![](img/c96f3a91dea1cd31c3383e2470fa9609_5.png)

## 核心思路与函数定义

![](img/c96f3a91dea1cd31c3383e2470fa9609_7.png)

![](img/c96f3a91dea1cd31c3383e2470fa9609_9.png)

我们的目标是：给定一组数据，计算其第一四分位数（Q1，25%）和第三四分位数（Q3，75%）。将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被“修剪”到了Q1和Q3之间的范围内。

以下是实现此功能的核心函数：

![](img/c96f3a91dea1cd31c3383e2470fa9609_11.png)

![](img/c96f3a91dea1cd31c3383e2470fa9609_13.png)

```python
import pandas as pd
import numpy as np

![](img/c96f3a91dea1cd31c3383e2470fa9609_15.png)

def winsorize_by_percentile(series, low_percentile=0.25, high_percentile=0.75):
    """
    基于百分位对序列进行去极值（缩尾）处理。
    
    参数:
    series (pd.Series): 待处理的序列数据。
    low_percentile (float): 下百分位，默认0.25（Q1）。
    high_percentile (float): 上百分位，默认0.75（Q3）。
    
    返回:
    pd.Series: 处理后的序列。
    """
    # 1. 对数据进行排序
    sorted_series = series.sort_values()
    
    # 2. 计算指定百分位的值
    # quantile函数返回指定分位数的值
    q_values = sorted_series.quantile([low_percentile, high_percentile])
    low_bound = q_values.iloc[0]  # 下界，如Q1
    high_bound = q_values.iloc[1] # 上界，如Q3
    
    # 3. 使用np.clip将数据限制在[low_bound, high_bound]范围内
    # 小于low_bound的值变为low_bound，大于high_bound的值变为high_bound
    clipped_series = np.clip(series, low_bound, high_bound)
    
    return clipped_series
```

![](img/c96f3a91dea1cd31c3383e2470fa9609_17.png)

![](img/c96f3a91dea1cd31c3383e2470fa9609_19.png)

## 代码步骤详解

![](img/c96f3a91dea1cd31c3383e2470fa9609_21.png)

现在，让我们一步步拆解这个函数的实现过程。

![](img/c96f3a91dea1cd31c3383e2470fa9609_23.png)

### 第一步：数据排序
为了计算百分位数，首先需要将数据按从小到大的顺序排列。我们使用Pandas Series的 `sort_values()` 方法。

```python
sorted_series = series.sort_values()
```

![](img/c96f3a91dea1cd31c3383e2470fa9609_25.png)

### 第二步：计算百分位边界值
我们使用Pandas的 `quantile()` 函数来计算指定百分位的具体数值。例如，传入 `[0.25, 0.75]` 会返回一个包含Q1和Q3值的Series。

![](img/c96f3a91dea1cd31c3383e2470fa9609_27.png)

```python
q_values = sorted_series.quantile([low_percentile, high_percentile])
low_bound = q_values.iloc[0]  # 例如，Q1的值
high_bound = q_values.iloc[1] # 例如，Q3的值
```

![](img/c96f3a91dea1cd31c3383e2470fa9609_29.png)

### 第三步：应用边界限制
使用NumPy的 `clip()` 函数，可以非常高效地将序列中的所有值限制在指定的最小值和最大值之间。

![](img/c96f3a91dea1cd31c3383e2470fa9609_31.png)

```python
clipped_series = np.clip(series, low_bound, high_bound)
```
`np.clip(array, min, max)` 函数的作用是：对于数组中的每个元素，如果它小于`min`，则将其设置为`min`；如果大于`max`，则将其设置为`max`；否则保持不变。

## 实际应用与效果对比

![](img/c96f3a91dea1cd31c3383e2470fa9609_33.png)

理解了函数原理后，我们将其应用于实际数据，并可视化处理前后的效果。

![](img/c96f3a91dea1cd31c3383e2470fa9609_35.png)

以下是应用示例和对比可视化的代码：

```python
import matplotlib.pyplot as plt

![](img/c96f3a91dea1cd31c3383e2470fa9609_37.png)

# 假设 `data` 是一个DataFrame，我们选择其中一列进行处理
column_name = ‘D‘  # 替换为你的实际列名
original_series = data[column_name]

![](img/c96f3a91dea1cd31c3383e2470fa9609_39.png)

![](img/c96f3a91dea1cd31c3383e2470fa9609_41.png)

# 应用去极值函数
# 注意：实际应用中，上下百分位常设置为如0.025和0.975，此处为演示效果使用0.25和0.75
winsorized_series = winsorize_by_percentile(original_series, 0.25, 0.75)

# 可视化对比
plt.figure(figsize=(10, 6))

![](img/c96f3a91dea1cd31c3383e2470fa9609_43.png)

# 绘制原始数据
plt.subplot(2, 1, 1)
plt.plot(original_series.values, ‘b-‘, label=‘Original Data‘)
plt.title(‘Original Data‘)
plt.legend()

![](img/c96f3a91dea1cd31c3383e2470fa9609_45.png)

# 绘制去极值后的数据
plt.subplot(2, 1, 2)
plt.plot(winsorized_series.values, ‘r-‘, label=‘Winsorized Data (0.25-0.75)‘)
plt.title(‘Data After Winsorization‘)
plt.legend()

![](img/c96f3a91dea1cd31c3383e2470fa9609_47.png)

plt.tight_layout()
plt.show()
```

![](img/c96f3a91dea1cd31c3383e2470fa9609_49.png)

通过上下两个子图的对比，可以清晰地看到，经过处理后的数据（下图）其波动范围被限制在了原始数据（上图）的Q1和Q3之间，两端的极端值被“修剪”掉了。

![](img/c96f3a91dea1cd31c3383e2470fa9609_51.png)

![](img/c96f3a91dea1cd31c3383e2470fa9609_53.png)

## 参数调整与实际应用建议

![](img/c96f3a91dea1cd31c3383e2470fa9609_55.png)

在演示中，我们使用了25%和75%作为边界，这使得处理效果非常明显。但在真实的金融因子分析或量化策略中，这个范围通常设置得更严格。

![](img/c96f3a91dea1cd31c3383e2470fa9609_57.png)

![](img/c96f3a91dea1cd31c3383e2470fa9609_59.png)

以下是实际应用中的常见参数选择：

![](img/c96f3a91dea1cd31c3383e2470fa9609_61.png)

*   **常用范围**：更常使用 **2.5%** 和 **97.5%** 作为上下边界（即 `low_percentile=0.025`, `high_percentile=0.975`）。这只会剔除头尾各2.5%的极端值，在控制异常值影响和保留数据信息之间取得更好平衡。
*   **灵活调整**：你可以根据具体数据特性和分析需求，灵活调整 `low_percentile` 和 `high_percentile` 参数。例如，对于波动性非常大的数据，可能需要更严格的修剪（如1%和99%）。

![](img/c96f3a91dea1cd31c3383e2470fa9609_63.png)

![](img/c96f3a91dea1cd31c3383e2470fa9609_65.png)

只需在调用函数时传入不同的参数即可：

![](img/c96f3a91dea1cd31c3383e2470fa9609_67.png)

```python
# 使用更常用的2.5%和97.5%分位进行去极值
winsorized_series_strict = winsorize_by_percentile(original_series, 0.025, 0.975)
```

![](img/c96f3a91dea1cd31c3383e2470fa9609_69.png)

![](img/c96f3a91dea1cd31c3383e2470fa9609_71.png)

本节课中我们一起学习了基于百分位进行去极值处理的完整流程。我们首先明确了方法的核心思想，即利用分位数确定数据的合理边界，并将超出边界的值替换为边界值。接着，我们定义了一个可复用的函数，详细解释了其排序、计算分位数和应用限制的三个关键步骤。最后，我们通过实际代码演示了处理效果，并讨论了参数调整在实际金融分析中的应用。掌握这种方法，能有效提升数据质量，为后续的因子分析和模型构建打下坚实基础。