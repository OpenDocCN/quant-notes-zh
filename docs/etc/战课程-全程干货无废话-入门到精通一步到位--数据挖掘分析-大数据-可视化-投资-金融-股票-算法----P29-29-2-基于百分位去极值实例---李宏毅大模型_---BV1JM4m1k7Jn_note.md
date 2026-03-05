# Python金融分析与量化交易实战：P29：基于百分位去极值实例 📊

![](img/c6367b1403e8962fd2ea2b0e560e94b5_1.png)

## 概述
在本节课程中，我们将学习一种在金融数据分析中常用的数据预处理技术——**基于百分位去极值**。我们将通过编写一个Python函数，将数据中超出指定百分位范围（例如，小于25%分位数或大于75%分位数）的极端值，替换为对应的边界值，从而使数据分布更加稳健。

![](img/c6367b1403e8962fd2ea2b0e560e94b5_3.png)

---

![](img/c6367b1403e8962fd2ea2b0e560e94b5_5.png)

## 核心思路与函数定义

![](img/c6367b1403e8962fd2ea2b0e560e94b5_7.png)

![](img/c6367b1403e8962fd2ea2b0e560e94b5_9.png)

上一节我们介绍了数据清洗的重要性，本节中我们来看看如何具体实现基于百分位的去极值方法。

我们的目标是：给定一个数据序列，计算其**第一四分位数（Q1）**和**第三四分位数（Q3）**，然后将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被规范到了一个由Q1和Q3定义的范围内。

![](img/c6367b1403e8962fd2ea2b0e560e94b5_11.png)

![](img/c6367b1403e8962fd2ea2b0e560e94b5_13.png)

以下是实现此功能的核心步骤，我们将它们封装在一个函数中：

![](img/c6367b1403e8962fd2ea2b0e560e94b5_15.png)

![](img/c6367b1403e8962fd2ea2b0e560e94b5_17.png)

```python
import pandas as pd
import numpy as np

![](img/c6367b1403e8962fd2ea2b0e560e94b5_19.png)

def winsorize_by_percentile(series, low_percentile=0.25, high_percentile=0.75):
    """
    基于百分位进行去极值（缩尾）处理。
    参数:
        series: pandas Series，待处理的数据列。
        low_percentile: float，下限百分位，例如0.25代表25%分位数。
        high_percentile: float，上限百分位，例如0.75代表75%分位数。
    返回:
        pandas Series，处理后的数据。
    """
    # 1. 对数据进行排序
    sorted_series = series.sort_values()

    # 2. 计算指定的百分位值
    # 使用quantile函数计算分位数
    q = sorted_series.quantile([low_percentile, high_percentile])
    low_bound = q.iloc[0]  # 获取下限值（如Q1）
    high_bound = q.iloc[1] # 获取上限值（如Q3）

    # 3. 使用np.clip将超出范围的值截断到边界
    # np.clip(array, min, max) 将array中小于min的值设为min，大于max的值设为max
    winsorized_series = np.clip(series, low_bound, high_bound)

    return winsorized_series
```

![](img/c6367b1403e8962fd2ea2b0e560e94b5_21.png)

---

## 代码应用与效果对比

函数定义完成后，我们就可以将其应用于实际的数据列。

![](img/c6367b1403e8962fd2ea2b0e560e94b5_23.png)

首先，我们指定要处理的列名。这里为了举例，我们随机选择一列，在实际分析中，这通常是某个需要清洗的因子或指标列。

![](img/c6367b1403e8962fd2ea2b0e560e94b5_25.png)

```python
# 假设`data`是一个包含多列数据的DataFrame
column_name = ‘D‘  # 指定要处理的列名
original_data = data[column_name]

![](img/c6367b1403e8962fd2ea2b0e560e94b5_27.png)

# 应用去极值函数
# 使用默认的25%和75%分位
processed_data = winsorize_by_percentile(original_data)
```

为了直观地观察去极值操作的效果，最好的方式是将处理前后的数据分布进行可视化对比。

![](img/c6367b1403e8962fd2ea2b0e560e94b5_29.png)

以下是绘制对比图的代码：

![](img/c6367b1403e8962fd2ea2b0e560e94b5_31.png)

```python
import matplotlib.pyplot as plt

# 创建一个包含两个子图的画布
fig, axes = plt.subplots(2, 1, figsize=(10, 8))

![](img/c6367b1403e8962fd2ea2b0e560e94b5_33.png)

# 绘制原始数据分布
axes[0].plot(original_data, ‘o‘, alpha=0.5, label=‘原始数据‘)
axes[0].set_title(‘原始数据分布‘)
axes[0].legend()

![](img/c6367b1403e8962fd2ea2b0e560e94b5_35.png)

# 绘制处理后的数据分布
axes[1].plot(processed_data, ‘o‘, alpha=0.5, color=‘orange‘, label=‘去极值后数据‘)
axes[1].set_title(‘基于百分位去极值后数据分布‘)
axes[1].legend()

![](img/c6367b1403e8962fd2ea2b0e560e94b5_37.png)

plt.tight_layout()
plt.show()
```

![](img/c6367b1403e8962fd2ea2b0e560e94b5_39.png)

通过对比上下两个图表，可以清晰地看到：
*   **上图（原始数据）**：数据点分布范围较广，可能存在远离主体区域的极端值。
*   **下图（处理后数据）**：所有数据点都被限制在了一个更窄的垂直范围内。原本低于Q1和高于Q3的点，现在分别被“拉回”到了Q1和Q3的水平线上，形成了两条明显的边界。

![](img/c6367b1403e8962fd2ea2b0e560e94b5_41.png)

---

![](img/c6367b1403e8962fd2ea2b0e560e94b5_43.png)

## 实际应用中的参数调整

![](img/c6367b1403e8962fd2ea2b0e560e94b5_45.png)

在刚才的实例中，我们使用了 **`low_percentile=0.25`** 和 **`high_percentile=0.75`** 作为边界。这在演示时效果明显，但在真实的金融因子分析中，这个范围可能过于宽松，会将太多正常数据视为异常。

![](img/c6367b1403e8962fd2ea2b0e560e94b5_47.png)

![](img/c6367b1403e8962fd2ea2b0e560e94b5_49.png)

在实际操作中，通常会使用更严格的百分位，例如：
*   **`low_percentile=0.025`** (2.5%分位数)
*   **`high_percentile=0.975`** (97.5%分位数)

![](img/c6367b1403e8962fd2ea2b0e560e94b5_51.png)

这意味着我们只将分布两端各2.5%的极端值进行缩尾处理，保留了中间95%的数据原貌。你可以通过修改函数调用时的参数来轻松调整这个范围：

![](img/c6367b1403e8962fd2ea2b0e560e94b5_53.png)

![](img/c6367b1403e8962fd2ea2b0e560e94b5_55.png)

```python
# 使用更常用的2.5%和97.5%分位进行去极值
processed_data_strict = winsorize_by_percentile(original_data, 0.025, 0.975)
```

![](img/c6367b1403e8962fd2ea2b0e560e94b5_57.png)

---

![](img/c6367b1403e8962fd2ea2b0e560e94b5_59.png)

![](img/c6367b1403e8962fd2ea2b0e560e94b5_61.png)

## 总结
本节课中我们一起学习了**基于百分位去极值**的完整实现流程。

![](img/c6367b1403e8962fd2ea2b0e560e94b5_63.png)

我们首先明确了目标：利用**分位数**（如Q1和Q3）界定数据的合理范围，并对超出范围的极值进行修正。接着，我们一步步实现了核心函数，其关键步骤包括：**数据排序**、**计算分位数边界**以及使用 **`np.clip`** 函数进行**数据截断**。最后，我们通过可视化对比，直观地验证了该方法的处理效果，并讨论了在实际金融分析中如何调整百分位参数以达到最佳效果。

![](img/c6367b1403e8962fd2ea2b0e560e94b5_65.png)

![](img/c6367b1403e8962fd2ea2b0e560e94b5_67.png)

这种方法能有效降低极端值对整体数据分析（如计算均值、方差）的影响，是构建稳健量化模型的重要预处理步骤。