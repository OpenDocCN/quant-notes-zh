# 金融分析与量化交易：02-2：基于百分位去极值实例 📊

![](img/aad97fdef2794538a8c7991eb213e6c3_1.png)

在本节课中，我们将学习如何使用分位数（百分位）法来处理金融数据中的极端值。这是一种常见的数据预处理技术，旨在将数据规范到一个合理的范围内，以减少异常值对后续分析（如因子分析、模型训练）的影响。

![](img/aad97fdef2794538a8c7991eb213e6c3_3.png)

上一节我们介绍了数据预处理的重要性，本节中我们来看看如何具体实现基于百分位的去极值方法。

![](img/aad97fdef2794538a8c7991eb213e6c3_5.png)

![](img/aad97fdef2794538a8c7991eb213e6c3_7.png)

## 核心思路与步骤

![](img/aad97fdef2794538a8c7991eb213e6c3_9.png)

我们的目标是：对于给定的一列数据，计算其第一四分位数（Q1，25%分位点）和第三四分位数（Q3，75%分位点）。然后，将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被“修剪”并规范到了[Q1, Q3]的区间内。

![](img/aad97fdef2794538a8c7991eb213e6c3_11.png)

以下是实现此功能的具体步骤：

![](img/aad97fdef2794538a8c7991eb213e6c3_13.png)

![](img/aad97fdef2794538a8c7991eb213e6c3_15.png)

1.  **选择数据列**：首先，我们需要从数据集中指定要进行处理的列。
2.  **编写处理函数**：创建一个函数，其核心逻辑是接收一个数据序列（Series）以及自定义的下限和上限分位点（例如0.25和0.75），然后返回处理后的序列。
3.  **函数内部操作**：
    *   对传入的数据序列进行排序。
    *   使用`pandas`的`quantile`函数计算指定分位点的具体数值。
    *   使用`numpy`的`clip`函数，将序列中超出[Q1, Q3]范围的值裁剪到边界上。
4.  **应用与可视化**：将编写好的函数应用到选定的数据列上，并通过绘制处理前后的数据分布图来直观对比效果。

![](img/aad97fdef2794538a8c7991eb213e6c3_17.png)

![](img/aad97fdef2794538a8c7991eb213e6c3_19.png)

## 代码实现详解

接下来，我们通过代码来一步步实现上述逻辑。

首先，我们假设已经有一个名为`data`的`DataFrame`，并选择其中一列进行操作。

```python
# 1. 选择要处理的列，例如名为‘column_D’的列
column_to_process = ‘column_D‘
series_to_process = data[column_to_process]
```

然后，我们编写核心的处理函数`winsorize_by_quantile`。

```python
import pandas as pd
import numpy as np

def winsorize_by_quantile(series, low_quantile=0.25, high_quantile=0.75):
    """
    基于分位数对序列进行去极值（缩尾）处理。
    参数:
        series: pandas Series, 待处理的数据序列。
        low_quantile: float, 下限分位数，默认0.25（Q1）。
        high_quantile: float, 上限分位数，默认0.75（Q3）。
    返回:
        pandas Series, 处理后的数据序列。
    """
    # 对数据排序（虽然对于quantile计算非必须，但有助于理解）
    sorted_series = series.sort_values()

    # 计算指定分位点的值
    # quantile函数接收一个分位数列表，返回对应的值列表
    quantile_values = sorted_series.quantile([low_quantile, high_quantile])

    # 提取下限值（Q1）和上限值（Q3）
    low_bound = quantile_values.iloc[0]  # 第一个值是下限分位点对应的值
    high_bound = quantile_values.iloc[1] # 第二个值是上限分位点对应的值

    # 使用np.clip将数据限制在[low_bound, high_bound]区间内
    # 小于low_bound的值变为low_bound，大于high_bound的值变为high_bound
    clipped_series = np.clip(series, low_bound, high_bound)

    return clipped_series
```

**代码关键点解释**：
*   `series.quantile([0.25, 0.75])`：这是`pandas`内置的高效方法，直接计算序列的25%和75%分位数。
*   `np.clip(series, a_min, a_max)`：这是`NumPy`的裁剪函数，是执行“小于下限的设为下限，大于上限的设为上限”这一操作的向量化实现，效率很高。

现在，我们可以应用这个函数来处理数据，并可视化结果。

![](img/aad97fdef2794538a8c7991eb213e6c3_21.png)

![](img/aad97fdef2794538a8c7991eb213e6c3_23.png)

```python
# 2. 应用去极值函数
# 使用默认的25%和75%分位点
processed_series = winsorize_by_quantile(series_to_process)

![](img/aad97fdef2794538a8c7991eb213e6c3_25.png)

# 3. 可视化对比
import matplotlib.pyplot as plt

![](img/aad97fdef2794538a8c7991eb213e6c3_27.png)

plt.figure(figsize=(12, 6))

![](img/aad97fdef2794538a8c7991eb213e6c3_29.png)

# 绘制原始数据分布
plt.subplot(2, 1, 1) # 创建2行1列的子图，当前激活第1个
series_to_process.plot(kind=‘hist‘, bins=50, alpha=0.7, color=‘blue‘, edgecolor=‘black‘)
plt.title(‘原始数据分布‘)
plt.xlabel(‘数值‘)
plt.ylabel(‘频数‘)

![](img/aad97fdef2794538a8c7991eb213e6c3_31.png)

# 绘制处理后的数据分布
plt.subplot(2, 1, 2) # 激活第2个子图
processed_series.plot(kind=‘hist‘, bins=50, alpha=0.7, color=‘green‘, edgecolor=‘black‘)
plt.title(‘基于百分位去极值后的数据分布‘)
plt.xlabel(‘数值‘)
plt.ylabel(‘频数‘)

![](img/aad97fdef2794538a8c7991eb213e6c3_33.png)

plt.tight_layout() # 自动调整子图参数，使之填充整个图像区域
plt.show()
```

![](img/aad97fdef2794538a8c7991eb213e6c3_35.png)

## 实际应用中的调整

![](img/aad97fdef2794538a8c7991eb213e6c3_37.png)

![](img/aad97fdef2794538a8c7991eb213e6c3_39.png)

在刚才的示例中，我们使用了**Q1（25%）**和**Q3（75%）**作为边界，这个范围较宽，主要是为了在图表上更明显地展示效果。

![](img/aad97fdef2794538a8c7991eb213e6c3_41.png)

![](img/aad97fdef2794538a8c7991eb213e6c3_43.png)

在实际的金融因子分析或量化策略中，我们通常会使用更严格的范围来定义“极端值”，例如：
*   **下限**：`low_quantile = 0.025` (2.5%分位点)
*   **上限**：`high_quantile = 0.975` (97.5%分位点)

![](img/aad97fdef2794538a8c7991eb213e6c3_45.png)

这意味着我们将数据中最小的2.5%和最大的2.5%视为异常值并进行处理。你可以通过向函数传递不同的参数来轻松调整这个范围。

![](img/aad97fdef2794538a8c7991eb213e6c3_47.png)

![](img/aad97fdef2794538a8c7991eb213e6c3_49.png)

```python
# 使用更常用的2.5%和97.5%分位点进行去极值
processed_series_strict = winsorize_by_quantile(series_to_process,
                                                 low_quantile=0.025,
                                                 high_quantile=0.975)
```

![](img/aad97fdef2794538a8c7991eb213e6c3_51.png)

![](img/aad97fdef2794538a8c7991eb213e6c3_53.png)

本节课中我们一起学习了基于百分位进行去极值处理的完整流程。我们首先明确了方法的核心思想——利用分位数确定数据的正常范围边界，并对超出边界的值进行截断。接着，我们通过编写一个清晰、可复用的函数`winsorize_by_quantile`实现了这一逻辑，其中关键点在于使用`pandas`的`quantile`函数计算分位点，以及使用`numpy`的`clip`函数进行高效裁剪。最后，我们通过可视化对比了处理前后的数据分布，并讨论了在实际应用中如何调整分位点参数以适应不同的场景。掌握这种方法，是进行稳健金融数据预处理的重要一步。