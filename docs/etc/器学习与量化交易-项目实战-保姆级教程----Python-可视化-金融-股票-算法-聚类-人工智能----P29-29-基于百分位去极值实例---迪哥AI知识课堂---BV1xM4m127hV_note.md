# 机器学习与量化交易：P29：基于百分位去极值实例 📊

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_1.png)

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_3.png)

在本节课中，我们将学习一种在数据预处理中常用的技术——基于百分位法去除极值。我们将通过一个具体的Python实例，演示如何将数据规范到一个指定的范围内，从而减少异常值对后续分析的影响。

## 概述

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_5.png)

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_7.png)

上一节我们介绍了数据中可能存在极值（异常值）的问题。本节中，我们来看看如何使用分位数（如Q1和Q3）来识别并处理这些极值。核心思想是：将所有小于下分位数（如25%分位数）的值设置为该分位数的值，将所有大于上分位数（如75%分位数）的值设置为该分位数的值，从而将数据“修剪”到一个合理的区间。

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_9.png)

## 操作步骤详解

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_11.png)

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_13.png)

以下是基于百分位去极值的主要步骤，我们将逐步实现。

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_15.png)

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_17.png)

### 第一步：选择目标数据列

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_19.png)

首先，我们需要指定要对哪一列数据进行操作。这通常是一个包含数值的Pandas Series。

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_21.png)

```python
# 假设我们有一个名为`data`的DataFrame，并选择其中一列，例如列名为‘D’
target_series = data['D']
```

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_23.png)

### 第二步：编写去极值函数

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_25.png)

接下来，我们需要编写一个函数来完成基于百分位的去极值操作。这个函数将接收一个数据序列（Series）以及我们想要保留的最小和最大百分位（如下四分位数和上四分位数）。

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_27.png)

以下是该函数的核心逻辑：

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_29.png)

1.  **对数据进行排序**：为了计算分位数，首先需要将数据按从小到大排序。
2.  **计算分位数值**：使用Pandas的`quantile`函数计算指定百分位（如下四分位数0.25和上四分位数0.75）对应的具体数值。
3.  **应用范围限制**：使用NumPy的`clip`函数，将序列中所有小于下分位数的值设置为下分位数，将所有大于上分位数的值设置为上分位数。

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_31.png)

```python
import pandas as pd
import numpy as np

def winsorize_by_percentile(series, low_percentile=0.25, high_percentile=0.75):
    """
    基于百分位法对序列进行去极值处理。

    参数:
    series (pd.Series): 待处理的数据序列。
    low_percentile (float): 下分位数，例如0.25代表25%分位数。
    high_percentile (float): 上分位数，例如0.75代表75%分位数。

    返回:
    pd.Series: 处理后的数据序列。
    """
    # 1. 对数据进行排序（虽然quantile函数内部会处理，但显式排序有助于理解逻辑）
    sorted_series = series.sort_values()

    # 2. 计算指定分位数的具体值
    # quantile函数接收一个列表，返回对应分位数的值
    q_values = sorted_series.quantile([low_percentile, high_percentile])
    low_bound = q_values.iloc[0]  # 下边界值（如Q1）
    high_bound = q_values.iloc[1] # 上边界值（如Q3）

    # 3. 使用clip函数将数据限制在[low_bound, high_bound]区间内
    # np.clip(series, a_min, a_max) 将series中小于a_min的值设为a_min，大于a_max的值设为a_max
    winsorized_series = np.clip(series, low_bound, high_bound)

    return winsorized_series
```

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_33.png)

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_35.png)

### 第三步：应用函数并可视化结果

函数编写完成后，我们可以将其应用到选定的数据列上，并通过绘图来直观对比处理前后的数据分布差异。

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_37.png)

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_39.png)

```python
import matplotlib.pyplot as plt

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_41.png)

# 应用去极值函数，这里使用默认的25%和75%分位数
winsorized_data = winsorize_by_percentile(data['D'])

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_43.png)

# 创建图形，上下排列两个子图以便对比
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(10, 8))

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_45.png)

# 绘制原始数据
ax1.plot(data['D'], label='原始数据')
ax1.set_title('原始数据分布')
ax1.legend()

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_47.png)

# 绘制去极值后的数据
ax2.plot(winsorized_data, label='去极值后数据', color='orange')
ax2.set_title('基于百分位去极值后数据分布')
ax2.legend()

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_49.png)

plt.tight_layout()
plt.show()
```

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_51.png)

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_53.png)

通过对比上下两个图表，可以清晰地看到，经过处理后的数据（下方图表）其波动范围被限制在了一个更窄的区间内。原本过高或过低的极值点都被“拉回”到了设定的边界（Q1和Q3）附近。

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_55.png)

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_57.png)

## 注意事项与扩展

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_59.png)

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_61.png)

在实际的量化交易或数据分析项目中，有几点需要注意：

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_63.png)

*   **分位数的选择**：本例中为了演示效果明显，使用了25%和75%分位数。在实际应用中，这个范围通常会更严格，例如使用**2.5%和97.5%**分位数（即 `low_percentile=0.025, high_percentile=0.975`），以避免将过多正常数据误判为极值。
*   **函数的灵活性**：我们编写的`winsorize_by_percentile`函数允许传入任意的`low_percentile`和`high_percentile`参数，因此可以根据不同因子或数据特征灵活调整去极值的严格程度。
*   **批量处理**：如果需要对DataFrame中的多个列进行同样的去极值操作，可以使用`apply`函数来批量处理。

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_65.png)

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_67.png)

## 总结

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_69.png)

![](img/c011d6dc8eadbf7c9050b2e8f6f7a0e7_71.png)

本节课中我们一起学习了基于百分位法进行数据去极值的完整流程。我们掌握了其核心思想：利用分位数确定数据的正常范围边界，并将超出边界的值替换为边界值。通过动手实现一个Python函数并可视化结果，我们不仅理解了该方法的原理，也获得了可直接应用于实际项目的代码工具。这种方法能有效平滑数据，降低异常值对模型训练的干扰，是数据预处理阶段一个非常实用的技巧。