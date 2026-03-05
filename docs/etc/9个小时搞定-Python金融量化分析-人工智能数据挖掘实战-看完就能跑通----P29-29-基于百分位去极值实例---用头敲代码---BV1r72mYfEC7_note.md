# Python金融量化分析：P29：基于百分位去极值实例 📊

![](img/4668d904e7e7ac723166df0c9d2548fa_1.png)

![](img/4668d904e7e7ac723166df0c9d2548fa_3.png)

在本节课中，我们将学习一种在金融数据分析中常用的数据预处理技术——基于百分位法去除极值。我们将通过编写一个函数，将数据规范到指定的百分位范围内，从而减少极端值对分析结果的影响。

![](img/4668d904e7e7ac723166df0c9d2548fa_5.png)

---

![](img/4668d904e7e7ac723166df0c9d2548fa_7.png)

![](img/4668d904e7e7ac723166df0c9d2548fa_9.png)

上一节我们介绍了数据清洗的重要性，本节中我们来看看如何具体实现基于百分位的去极值方法。

![](img/4668d904e7e7ac723166df0c9d2548fa_11.png)

![](img/4668d904e7e7ac723166df0c9d2548fa_13.png)

我们的目标是：给定一个数据序列，计算其第一四分位数（Q1，25%）和第三四分位数（Q3，75%）。然后，将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被“修剪”并规范到了一个更稳定的范围内。

![](img/4668d904e7e7ac723166df0c9d2548fa_15.png)

![](img/4668d904e7e7ac723166df0c9d2548fa_17.png)

以下是实现此功能的核心步骤：

![](img/4668d904e7e7ac723166df0c9d2548fa_19.png)

1.  **数据排序**：首先，需要对数据进行排序，以便准确找到指定百分位对应的数值。
2.  **计算分位点**：使用Pandas库的函数计算Q1和Q3的值。
3.  **数据裁剪**：使用NumPy库的`clip`函数，将数据限制在`[Q1, Q3]`的区间内。

![](img/4668d904e7e7ac723166df0c9d2548fa_21.png)

![](img/4668d904e7e7ac723166df0c9d2548fa_23.png)

接下来，我们将按照这些步骤编写代码。

首先，我们定义一个函数`clip_by_percentile`。这个函数接收三个参数：一个Pandas Series类型的数据列`series`，以及希望保留的最小百分位`low_percent`和最大百分位`high_percent`。

![](img/4668d904e7e7ac723166df0c9d2548fa_25.png)

![](img/4668d904e7e7ac723166df0c9d2548fa_27.png)

```python
import pandas as pd
import numpy as np

![](img/4668d904e7e7ac723166df0c9d2548fa_29.png)

def clip_by_percentile(series, low_percent=0.25, high_percent=0.75):
    """
    基于百分位法去除极值。
    参数:
        series: Pandas Series，待处理的数据列。
        low_percent: 浮点数，下限百分位（如0.25代表25%）。
        high_percent: 浮点数，上限百分位（如0.75代表75%）。
    返回:
        处理后的Pandas Series。
    """
    # 1. 对数据进行排序（虽然pandas的quantile函数内部会处理，但显式排序逻辑更清晰）
    sorted_series = series.sort_values()

    # 2. 计算指定百分位对应的值（即Q1和Q3）
    # 使用quantile函数，传入一个列表[low_percent, high_percent]
    q_values = sorted_series.quantile([low_percent, high_percent])

    # q_values是一个Series，索引0对应low_percent的值，索引1对应high_percent的值
    low_bound = q_values.iloc[0]  # 例如 Q1
    high_bound = q_values.iloc[1] # 例如 Q3

    # 3. 使用np.clip将数据裁剪到[low_bound, high_bound]范围内
    # np.clip(array, a_min, a_max) 将array中小于a_min的值设为a_min，大于a_max的值设为a_max
    clipped_series = np.clip(series, low_bound, high_bound)

    return clipped_series
```

![](img/4668d904e7e7ac723166df0c9d2548fa_31.png)

代码解析：
*   `series.sort_values()`: 对输入的数据进行升序排序。
*   `series.quantile([low_percent, high_percent])`: 这是Pandas的核心函数，用于计算分位数。传入一个百分位列表，它会返回对应位置的值。
*   `np.clip(series, low_bound, high_bound)`: 这是NumPy的核心函数，用于将数组中的所有元素限制在给定的最小值和最大值之间。

函数编写完成后，我们可以将其应用于实际数据。假设我们有一个名为`data`的DataFrame，其中包含一列名为`‘D’`的数据。

![](img/4668d904e7e7ac723166df0c9d2548fa_33.png)

```python
# 假设data是一个Pandas DataFrame，并且有一列名为‘D’
column_to_clip = ‘D‘
original_data = data[column_to_clip]

![](img/4668d904e7e7ac723166df0c9d2548fa_35.png)

# 应用我们的去极值函数
# 使用默认的25%和75%作为边界
clipped_data = clip_by_percentile(original_data)

![](img/4668d904e7e7ac723166df0c9d2548fa_37.png)

![](img/4668d904e7e7ac723166df0c9d2548fa_39.png)

# 也可以自定义边界，例如使用2.5%和97.5%（更常用的窄范围）
# clipped_data = clip_by_percentile(original_data, low_percent=0.025, high_percent=0.975)
```

![](img/4668d904e7e7ac723166df0c9d2548fa_41.png)

为了直观地对比处理前后的效果，我们可以将原始数据和去极值后的数据绘制成图表。

![](img/4668d904e7e7ac723166df0c9d2548fa_43.png)

```python
import matplotlib.pyplot as plt

![](img/4668d904e7e7ac723166df0c9d2548fa_45.png)

# 创建画布和子图
fig, axes = plt.subplots(2, 1, figsize=(10, 8))

![](img/4668d904e7e7ac723166df0c9d2548fa_47.png)

![](img/4668d904e7e7ac723166df0c9d2548fa_49.png)

# 绘制原始数据
axes[0].plot(original_data.values, label=‘Original Data‘, color=‘blue‘, alpha=0.7)
axes[0].set_title(‘Original Data‘)
axes[0].legend()
axes[0].grid(True)

![](img/4668d904e7e7ac723166df0c9d2548fa_51.png)

# 绘制去极值后的数据
axes[1].plot(clipped_data.values, label=‘Clipped Data (25%-75%)‘, color=‘red‘, alpha=0.7)
axes[1].set_title(‘Data After Clipping‘)
axes[1].legend()
axes[1].grid(True)

![](img/4668d904e7e7ac723166df0c9d2548fa_53.png)

![](img/4668d904e7e7ac723166df0c9d2548fa_55.png)

![](img/4668d904e7e7ac723166df0c9d2548fa_57.png)

plt.tight_layout()
plt.show()
```

![](img/4668d904e7e7ac723166df0c9d2548fa_59.png)

![](img/4668d904e7e7ac723166df0c9d2548fa_61.png)

通过图表可以清晰地看到，经过`clip`操作后，原来分布在两端（低于Q1和高于Q3）的异常点被“拉回”到了边界值（Q1和Q3）上，数据的波动范围被显著缩小，整体分布更加集中。

![](img/4668d904e7e7ac723166df0c9d2548fa_63.png)

![](img/4668d904e7e7ac723166df0c9d2548fa_65.png)

---

![](img/4668d904e7e7ac723166df0c9d2548fa_67.png)

![](img/4668d904e7e7ac723166df0c9d2548fa_69.png)

本节课中我们一起学习了基于百分位法去除数据极值的完整流程。我们首先明确了将数据规范到Q1和Q3之间的目标，然后通过**数据排序**、**计算分位点**和**数据裁剪**三个步骤实现了核心函数。在实际应用中，可以根据分析需求调整`low_percent`和`high_percent`参数，例如使用`0.025`和`0.975`来定义一个更窄、更严格的正常值范围。这种方法能有效抑制极端值的影响，为后续的金融因子分析和建模提供更稳健的数据基础。