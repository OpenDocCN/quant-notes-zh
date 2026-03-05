# Python金融时间序列分析与量化交易实战教程：P26：25. 基于百分位去极值实例 📊

![](img/6995b7d6c5498df72fe7017453231031_1.png)

![](img/6995b7d6c5498df72fe7017453231031_3.png)

在本节课中，我们将学习如何基于百分位法对金融数据进行去极值处理。这是一种常见的数据预处理技术，旨在将数据规范到合理的范围内，减少极端值对后续分析的影响。

![](img/6995b7d6c5498df72fe7017453231031_5.png)

## 概述

![](img/6995b7d6c5498df72fe7017453231031_7.png)

![](img/6995b7d6c5498df72fe7017453231031_9.png)

上一节我们介绍了数据预处理的重要性，本节中我们来看看如何具体实现基于百分位的去极值方法。其核心思想是：计算数据的下四分位数（Q1）和上四分位数（Q3），然后将所有小于Q1的值替换为Q1，将所有大于Q3的值替换为Q3，从而将数据“修剪”到Q1和Q3之间的范围内。

![](img/6995b7d6c5498df72fe7017453231031_11.png)

![](img/6995b7d6c5498df72fe7017453231031_13.png)

## 实现步骤

![](img/6995b7d6c5498df72fe7017453231031_15.png)

![](img/6995b7d6c5498df72fe7017453231031_17.png)

以下是实现基于百分位去极值操作的具体步骤。

![](img/6995b7d6c5498df72fe7017453231031_19.png)

![](img/6995b7d6c5498df72fe7017453231031_21.png)

### 1. 选择目标数据列

![](img/6995b7d6c5498df72fe7017453231031_23.png)

![](img/6995b7d6c5498df72fe7017453231031_25.png)

首先，我们需要指定要对哪一列数据进行操作。在本例中，我们随机选择一列作为演示。

![](img/6995b7d6c5498df72fe7017453231031_27.png)

```python
column_name = ‘your_column_name’  # 替换为实际的列名
data_series = data[column_name]
```

![](img/6995b7d6c5498df72fe7017453231031_29.png)

### 2. 定义去极值函数

![](img/6995b7d6c5498df72fe7017453231031_31.png)

![](img/6995b7d6c5498df72fe7017453231031_33.png)

接下来，我们编写一个函数来完成核心的去极值操作。这个函数将接收一个数据序列（Series），以及我们希望保留的最小和最大百分位值。

![](img/6995b7d6c5498df72fe7017453231031_35.png)

```python
def winsorize_by_percentile(series, low_percentile=0.25, high_percentile=0.75):
    “””
    基于百分位进行去极值处理。
    参数:
        series: 待处理的数据序列 (pandas Series)。
        low_percentile: 下百分位，默认0.25 (Q1)。
        high_percentile: 上百分位，默认0.75 (Q3)。
    返回:
        处理后的数据序列。
    “””
    # 第一步：对数据进行排序
    sorted_series = series.sort_values()
    
    # 第二步：计算指定百分位对应的实际数值
    # 使用 quantile 函数计算分位数
    q_values = sorted_series.quantile([low_percentile, high_percentile])
    low_bound = q_values.iloc[0]  # 下边界值 (如Q1)
    high_bound = q_values.iloc[1] # 上边界值 (如Q3)
    
    # 第三步：使用 clip 函数将数据规范到 [low_bound, high_bound] 区间内
    # 所有小于 low_bound 的值变为 low_bound，所有大于 high_bound 的值变为 high_bound
    winsorized_series = series.clip(lower=low_bound, upper=high_bound)
    
    return winsorized_series
```

![](img/6995b7d6c5498df72fe7017453231031_37.png)

![](img/6995b7d6c5498df72fe7017453231031_39.png)

**核心函数解释：**
*   `series.sort_values()`: 将数据按值从小到大排序，这是计算准确分位数的前提。
*   `series.quantile([a, b])`: Pandas函数，用于计算序列在a和b分位处的值。例如，`quantile([0.25, 0.75])` 返回Q1和Q3的值。
*   `series.clip(lower, upper)`: NumPy/Pandas函数，将序列中所有小于`lower`的值设置为`lower`，所有大于`upper`的值设置为`upper`。这正是我们需要的“修剪”操作。

![](img/6995b7d6c5498df72fe7017453231031_41.png)

![](img/6995b7d6c5498df72fe7017453231031_43.png)

### 3. 应用函数并可视化结果

![](img/6995b7d6c5498df72fe7017453231031_45.png)

![](img/6995b7d6c5498df72fe7017453231031_47.png)

函数定义好后，我们可以将其应用到选定的数据列上，并通过绘图对比处理前后的效果。

```python
# 应用去极值函数，这里使用默认的25%和75%分位
winsorized_data = winsorize_by_percentile(data_series, low_percentile=0.25, high_percentile=0.75)

![](img/6995b7d6c5498df72fe7017453231031_49.png)

![](img/6995b7d6c5498df72fe7017453231031_51.png)

# 可视化对比
import matplotlib.pyplot as plt

![](img/6995b7d6c5498df72fe7017453231031_53.png)

plt.figure(figsize=(12, 6))

![](img/6995b7d6c5498df72fe7017453231031_55.png)

![](img/6995b7d6c5498df72fe7017453231031_57.png)

# 绘制原始数据
plt.subplot(2, 1, 1) # 创建2行1列的子图，当前为第1个
plt.plot(data_series.values, label=‘Original Data’)
plt.title(‘Original Data’)
plt.legend()

![](img/6995b7d6c5498df72fe7017453231031_59.png)

![](img/6995b7d6c5498df72fe7017453231031_61.png)

# 绘制去极值后的数据
plt.subplot(2, 1, 2) # 当前为第2个子图
plt.plot(winsorized_data.values, label=‘Winsorized Data’, color=‘orange’)
plt.title(‘Data After Winsorization (25%-75%)’)
plt.legend()

![](img/6995b7d6c5498df72fe7017453231031_63.png)

plt.tight_layout() # 自动调整子图参数，使之填充整个图像区域
plt.show()
```

![](img/6995b7d6c5498df72fe7017453231031_65.png)

![](img/6995b7d6c5498df72fe7017453231031_67.png)

## 注意事项与扩展

![](img/6995b7d6c5498df72fe7017453231031_69.png)

![](img/6995b7d6c5498df72fe7017453231031_71.png)

在完成了基本操作后，有几点需要注意：

![](img/6995b7d6c5498df72fe7017453231031_73.png)

![](img/6995b7d6c5498df72fe7017453231031_75.png)

![](img/6995b7d6c5498df72fe7017453231031_77.png)

1.  **分位数的选择**：本例中使用25%和75%分位（即四分位距）是为了演示效果明显。在实际金融因子分析中，为了更严格地剔除极值，通常会选择更极端的分位点，例如 **2.5%和97.5%**，或 **1%和99%**。只需修改函数的 `low_percentile` 和 `high_percentile` 参数即可。
2.  **结果解读**：从可视化图形可以看出，处理后的数据（下方子图）的“尖峰”被削平了，数据被限制在了一个更窄的带状区间内。原来低于Q1和高于Q3的异常点都被拉回到了边界上。

![](img/6995b7d6c5498df72fe7017453231031_79.png)

![](img/6995b7d6c5498df72fe7017453231031_81.png)

## 总结

![](img/6995b7d6c5498df72fe7017453231031_83.png)

![](img/6995b7d6c5498df72fe7017453231031_85.png)

本节课中我们一起学习了基于百分位进行去极值处理的完整流程。我们掌握了其核心思想，即通过分位数确定数据的正常范围边界，并使用 `clip` 函数进行数据修剪。我们不仅实现了具体的函数，还通过可视化对比直观地看到了处理效果。这是金融数据预处理中非常实用且关键的一步，能为后续的模型构建提供更干净、更稳定的数据基础。在实际应用中，请根据数据特性和分析目标灵活调整分位点的选择。