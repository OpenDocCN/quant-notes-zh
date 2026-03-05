# Python金融量化交易：29：基于百分位去极值实例 📊

![](img/e1a597a803ad16952854dbe77078d820_1.png)

![](img/e1a597a803ad16952854dbe77078d820_3.png)

在本节课中，我们将学习如何使用百分位法（例如，基于Q1和Q3）来去除数据集中的极端值。这是一种常见的数据预处理技术，在金融量化分析中用于处理异常数据，使模型更加稳健。

![](img/e1a597a803ad16952854dbe77078d820_5.png)

## 概述

![](img/e1a597a803ad16952854dbe77078d820_7.png)

![](img/e1a597a803ad16952854dbe77078d820_9.png)

上一节我们介绍了数据预处理的重要性，本节中我们来看看如何具体实现基于百分位的去极值方法。我们将编写一个函数，将指定列中低于下四分位数（Q1）的值设置为Q1，高于上四分位数（Q3）的值设置为Q3，从而将数据规范到一个合理的范围内。

![](img/e1a597a803ad16952854dbe77078d820_11.png)

![](img/e1a597a803ad16952854dbe77078d820_13.png)

## 实现步骤

![](img/e1a597a803ad16952854dbe77078d820_15.png)

![](img/e1a597a803ad16952854dbe77078d820_17.png)

以下是实现基于百分位去极值功能的具体步骤。

![](img/e1a597a803ad16952854dbe77078d820_19.png)

### 1. 选择目标数据列

![](img/e1a597a803ad16952854dbe77078d820_21.png)

首先，我们需要指定要对数据框（DataFrame）中的哪一列进行操作。

```python
column_name = ‘D‘  # 示例列名，实际操作中可替换为具体因子列
```

![](img/e1a597a803ad16952854dbe77078d820_23.png)

![](img/e1a597a803ad16952854dbe77078d820_25.png)

### 2. 编写去极值函数

![](img/e1a597a803ad16952854dbe77078d820_27.png)

![](img/e1a597a803ad16952854dbe77078d820_29.png)

接下来，我们编写一个核心函数 `winsorize_by_percentile`。该函数接收一个Pandas Series数据、一个最小百分位和一个最大百分位作为参数。

函数内部逻辑如下：
*   **数据排序**：为了计算分位数，首先需要对数据进行排序。
*   **计算边界值**：使用 `series.quantile()` 方法计算指定百分位对应的具体数值。
*   **数据规约**：利用 `np.clip()` 函数，将序列中所有小于下限的值设置为下限，所有大于上限的值设置为上限。

![](img/e1a597a803ad16952854dbe77078d820_31.png)

```python
import pandas as pd
import numpy as np

def winsorize_by_percentile(series, low_percentile, high_percentile):
    """
    基于百分位进行去极值处理
    参数:
        series: 待处理的数据序列 (Pandas Series)
        low_percentile: 下限百分位 (如 0.25 代表 Q1)
        high_percentile: 上限百分位 (如 0.75 代表 Q3)
    返回:
        处理后的数据序列 (Pandas Series)
    """
    # 计算指定百分位对应的值
    q = series.quantile([low_percentile, high_percentile])
    low_bound = q.iloc[0]  # 下限值
    high_bound = q.iloc[1] # 上限值

    # 使用np.clip将数据限制在[low_bound, high_bound]范围内
    winsorized_series = np.clip(series, low_bound, high_bound)
    return winsorized_series
```

![](img/e1a597a803ad16952854dbe77078d820_33.png)

![](img/e1a597a803ad16952854dbe77078d820_35.png)

### 3. 应用函数并可视化结果

![](img/e1a597a803ad16952854dbe77078d820_37.png)

函数编写完成后，我们可以将其应用到选定的数据列上，并通过绘图对比处理前后的数据分布，以直观展示去极值的效果。

![](img/e1a597a803ad16952854dbe77078d820_39.png)

```python
import matplotlib.pyplot as plt

![](img/e1a597a803ad16952854dbe77078d820_41.png)

![](img/e1a597a803ad16952854dbe77078d820_43.png)

# 假设 `data` 是一个包含多列数据的DataFrame
original_series = data[column_name]
winsorized_series = winsorize_by_percentile(original_series, 0.25, 0.75)

![](img/e1a597a803ad16952854dbe77078d820_45.png)

# 绘制对比图
plt.figure(figsize=(10, 6))
plt.plot(original_series, label=‘原始数据‘, alpha=0.7)
plt.plot(winsorized_series, label=‘去极值后数据‘, alpha=0.7)
plt.legend()
plt.title(‘基于百分位(Q1, Q3)去极值效果对比‘)
plt.show()
```

![](img/e1a597a803ad16952854dbe77078d820_47.png)

![](img/e1a597a803ad16952854dbe77078d820_49.png)

绘图结果将显示，原始数据中超出[Q1, Q3]范围的极端点（异常高或异常低）被“拉回”到边界上，形成了平台效应，从而实现了数据的平滑处理。

![](img/e1a597a803ad16952854dbe77078d820_51.png)

![](img/e1a597a803ad16952854dbe77078d820_53.png)

## 注意事项

![](img/e1a597a803ad16952854dbe77078d820_55.png)

![](img/e1a597a803ad16952854dbe77078d820_57.png)

![](img/e1a597a803ad16952854dbe77078d820_59.png)

在实际的金融因子分析中，通常不会使用像（0.25, 0.75）这样宽泛的范围，因为这会将过多正常数据判定为异常。更常见的做法是使用更严格的百分位，例如（0.025, 0.975）或（0.05, 0.95），以剔除真正意义上的极端异常值。本节为了在图表上更清晰地展示效果，故而选择了较大的范围。

![](img/e1a597a803ad16952854dbe77078d820_61.png)

![](img/e1a597a803ad16952854dbe77078d820_63.png)

## 总结

![](img/e1a597a803ad16952854dbe77078d820_65.png)

![](img/e1a597a803ad16952854dbe77078d820_67.png)

本节课中我们一起学习了基于百分位法去除数据极值的完整流程。我们掌握了如何编写一个通用的去极值函数，其核心步骤包括**数据排序**、**计算分位点**以及**使用`np.clip`进行数据规约**。这种方法简单有效，是量化数据预处理中标准化、规范化数据分布的重要手段。理解并掌握它，能为后续构建更稳健的量化模型打下坚实基础。