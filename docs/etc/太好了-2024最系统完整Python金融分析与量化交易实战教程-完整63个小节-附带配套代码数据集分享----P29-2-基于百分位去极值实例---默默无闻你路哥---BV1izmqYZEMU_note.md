# Python金融分析与量化交易实战教程：P29：基于百分位去极值实例 📊

![](img/16cabf9b17b8574d722131e1d769d02c_1.png)

在本节课中，我们将学习如何使用百分位法对金融数据进行去极值处理。这是一种常见的数据预处理技术，旨在将数据规范到合理的范围内，减少极端值对分析结果的影响。

![](img/16cabf9b17b8574d722131e1d769d02c_3.png)

上一节我们介绍了数据预处理的重要性，本节中我们来看看如何具体实现基于百分位的去极值方法。

![](img/16cabf9b17b8574d722131e1d769d02c_5.png)

![](img/16cabf9b17b8574d722131e1d769d02c_7.png)

## 核心思路 💡

![](img/16cabf9b17b8574d722131e1d769d02c_9.png)

我们的目标是：给定一个数据序列，计算其第一四分位数（Q1）和第三四分位数（Q3）。将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被“裁剪”到了Q1和Q3之间的范围内。

![](img/16cabf9b17b8574d722131e1d769d02c_11.png)

## 实现步骤 🛠️

![](img/16cabf9b17b8574d722131e1d769d02c_13.png)

![](img/16cabf9b17b8574d722131e1d769d02c_15.png)

以下是实现基于百分位去极值的主要步骤。

![](img/16cabf9b17b8574d722131e1d769d02c_17.png)

### 1. 定义处理函数

![](img/16cabf9b17b8574d722131e1d769d02c_19.png)

![](img/16cabf9b17b8574d722131e1d769d02c_21.png)

首先，我们需要编写一个函数来完成核心的去极值操作。这个函数接收三个参数：一个Pandas Series数据序列，以及我们希望保留的最小和最大百分位。

![](img/16cabf9b17b8574d722131e1d769d02c_23.png)

```python
import pandas as pd
import numpy as np

def winsorize_by_percentile(series, min_percentile, max_percentile):
    """
    基于百分位对数据进行去极值处理（缩尾处理）。
    参数:
        series: Pandas Series，待处理的数据列。
        min_percentile: 最小百分位，例如0.25。
        max_percentile: 最大百分位，例如0.75。
    返回:
        处理后的Pandas Series。
    """
    # 对数据进行排序，为计算分位数做准备
    sorted_series = series.sort_values()

    # 计算指定百分位对应的值
    # quantile函数可以直接计算分位数
    q_values = sorted_series.quantile([min_percentile, max_percentile])

    # 获取最小和最大边界值
    min_boundary = q_values.iloc[0]  # 对应min_percentile的值
    max_boundary = q_values.iloc[1]  # 对应max_percentile的值

    # 使用np.clip函数将数据限制在[min_boundary, max_boundary]范围内
    # 小于min_boundary的值变为min_boundary，大于max_boundary的值变为max_boundary
    clipped_series = np.clip(series, min_boundary, max_boundary)

    return clipped_series
```

![](img/16cabf9b17b8574d722131e1d769d02c_25.png)

### 2. 应用函数处理数据

![](img/16cabf9b17b8574d722131e1d769d02c_27.png)

定义好函数后，我们可以将其应用于具体的金融数据列。假设我们有一个名为`data`的DataFrame，其中包含一列名为`‘D’`的数据。

![](img/16cabf9b17b8574d722131e1d769d02c_29.png)

```python
# 假设data是一个包含金融数据的DataFrame
# 对‘D’列进行去极值处理，保留25%到75%之间的数据
processed_series = winsorize_by_percentile(data['D'], 0.25, 0.75)

![](img/16cabf9b17b8574d722131e1d769d02c_31.png)

# 将处理后的数据添加回原DataFrame或进行后续分析
data['D_winsorized'] = processed_series
```

### 3. 可视化对比结果

![](img/16cabf9b17b8574d722131e1d769d02c_33.png)

为了直观地观察去极值处理的效果，我们可以将原始数据和处理后的数据绘制在同一张图上进行对比。

```python
import matplotlib.pyplot as plt

![](img/16cabf9b17b8574d722131e1d769d02c_35.png)

# 创建一个图形，包含两个子图上下排列
fig, axes = plt.subplots(2, 1, figsize=(10, 8))

![](img/16cabf9b17b8574d722131e1d769d02c_37.png)

![](img/16cabf9b17b8574d722131e1d769d02c_39.png)

# 在上方子图中绘制原始数据
axes[0].plot(data['D'], label='原始数据', color='blue', alpha=0.7)
axes[0].set_title('原始数据分布')
axes[0].legend()
axes[0].grid(True, linestyle='--', alpha=0.5)

# 在下方子图中绘制处理后的数据
axes[1].plot(data['D_winsorized'], label='去极值后数据', color='orange', alpha=0.7)
axes[1].set_title('基于百分位去极值后数据分布')
axes[1].legend()
axes[1].grid(True, linestyle='--', alpha=0.5)

![](img/16cabf9b17b8574d722131e1d769d02c_41.png)

plt.tight_layout()
plt.show()
```

![](img/16cabf9b17b8574d722131e1d769d02c_43.png)

![](img/16cabf9b17b8574d722131e1d769d02c_45.png)

通过对比上下两个图表，可以清晰地看到，经过处理后，数据的上下边界被限制在了计算出的Q1和Q3值处。原本低于Q1的“尾巴”被抬升到了Q1的水平线，高于Q3的“尾巴”被压低到了Q3的水平线，数据整体被规范到了一个更集中的范围内。

![](img/16cabf9b17b8574d722131e1d769d02c_47.png)

## 注意事项与参数调整 ⚙️

![](img/16cabf9b17b8574d722131e1d769d02c_49.png)

在实际的金融因子分析中，直接使用25%和75%作为边界可能过于宽松，会将太多正常数据视为异常值。通常，我们会使用更严格的百分位，例如：

![](img/16cabf9b17b8574d722131e1d769d02c_51.png)

*   **常用参数**：`min_percentile=0.025`, `max_percentile=0.975`
*   **含义**：仅将分布两端各2.5%的极端数据视为异常值并进行处理。

![](img/16cabf9b17b8574d722131e1d769d02c_53.png)

![](img/16cabf9b17b8574d722131e1d769d02c_55.png)

你可以根据具体数据的特点和分析需求，灵活调整`min_percentile`和`max_percentile`参数。本节课的例子为了在图表上展示更明显的效果，有意使用了较宽的范围。

![](img/16cabf9b17b8574d722131e1d769d02c_57.png)

![](img/16cabf9b17b8574d722131e1d769d02c_59.png)

## 总结 📝

![](img/16cabf9b17b8574d722131e1d769d02c_61.png)

本节课中我们一起学习了基于百分位进行去极值处理的方法。我们掌握了其核心思想：**利用分位数确定数据的正常范围边界，并将超出边界的极端值替换为边界值**。

![](img/16cabf9b17b8574d722131e1d769d02c_63.png)

![](img/16cabf9b17b8574d722131e1d769d02c_65.png)

我们通过三个步骤实现了它：
1.  **定义函数**：编写了`winsorize_by_percentile`函数，其核心是`series.quantile()`和`np.clip()`的运用。
2.  **应用处理**：对选定的数据列调用该函数，得到处理后的序列。
3.  **效果验证**：通过可视化对比，直观确认了处理将数据规范到了指定百分位确定的范围内。

![](img/16cabf9b17b8574d722131e1d769d02c_67.png)

![](img/16cabf9b17b8574d722131e1d769d02c_69.png)

这种方法简单有效，是金融数据预处理中标准化、消除极端值影响的常用工具之一。记住，关键在于根据实际情况选择合适的百分位参数。