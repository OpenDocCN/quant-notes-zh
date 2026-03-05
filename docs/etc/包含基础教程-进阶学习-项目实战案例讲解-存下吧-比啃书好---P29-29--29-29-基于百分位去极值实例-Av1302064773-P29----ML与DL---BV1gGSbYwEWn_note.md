# 量化交易教程：P29：基于百分位去极值实例 📊

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_1.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_3.png)

在本节课中，我们将学习一种在金融数据分析中常用的数据预处理技术：**基于百分位去极值**。我们将通过一个具体的Python实例，演示如何识别并处理数据集中的极端值，使数据分布更加稳健，为后续的量化分析打下基础。

## 核心思路与目标 🎯

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_5.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_7.png)

上一节我们介绍了数据清洗的重要性，本节中我们来看看如何利用百分位数来识别和处理异常值（极值）。我们的目标是：给定一个数据集，计算其第一四分位数（Q1，25%）和第三四分位数（Q3，75%），然后将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样可以将数据规范到一个更稳定的范围内，减少极端值对分析结果的影响。

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_9.png)

## 分步实现教程 🔧

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_11.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_13.png)

以下是实现基于百分位去极值功能的具体步骤。

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_15.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_17.png)

### 第一步：选择目标数据列

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_19.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_21.png)

首先，我们需要从数据集中指定一列来进行操作。这通常是你认为可能存在异常值或需要进行标准化处理的因子或特征列。

```python
# 假设 `data` 是一个Pandas DataFrame
column_name = ‘D‘  # 这里以‘D‘列为例
selected_series = data[column_name]
```

### 第二步：编写去极值函数

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_23.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_25.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_27.png)

接下来，我们需要编写一个核心函数。这个函数接收一个数据序列（Series）以及我们希望保留的最小和最大百分位（例如，0.25和0.75），然后返回处理后的序列。

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_29.png)

函数内部逻辑如下：
1.  **排序数据**：为了准确计算百分位数，需要先将数据按升序排列。
2.  **计算边界值**：使用Pandas的`quantile`函数计算指定百分位对应的具体数值（即Q1和Q3）。
3.  **裁剪数据**：使用NumPy的`clip`函数，将序列中所有小于Q1的值替换为Q1，所有大于Q3的值替换为Q3。

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_31.png)

```python
import pandas as pd
import numpy as np

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_33.png)

def winsorize_by_percentile(series, low_percentile=0.25, high_percentile=0.75):
    “““
    基于百分位进行去极值（缩尾）处理。
    参数:
        series: Pandas Series，待处理的数据序列。
        low_percentile: 下限百分位，例如0.25代表第一四分位数。
        high_percentile: 上限百分位，例如0.75代表第三四分位数。
    返回:
        处理后的Pandas Series。
    “““
    # 1. 对数据进行排序
    sorted_series = series.sort_values()
    # 2. 计算指定百分位的边界值
    q_low = sorted_series.quantile(low_percentile)
    q_high = sorted_series.quantile(high_percentile)
    # 3. 使用clip函数将数据限制在[Q1, Q3]范围内
    winsorized_series = np.clip(series, q_low, q_high)
    return winsorized_series
```

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_35.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_37.png)

### 第三步：应用函数并可视化结果

函数编写完成后，我们可以将其应用到选定的数据列上，并通过绘图来直观对比处理前后的数据分布差异。

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_39.png)

```python
import matplotlib.pyplot as plt

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_41.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_43.png)

# 应用去极值函数，这里使用默认的25%和75%分位
winsorized_data = winsorize_by_percentile(selected_series)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_45.png)

# 创建图形对比原始数据与处理后的数据
plt.figure(figsize=(12, 6))

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_47.png)

# 绘制原始数据
plt.subplot(2, 1, 1)  # 2行1列，第1个图
selected_series.plot(title=‘原始数据分布‘)
plt.grid(True)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_49.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_51.png)

# 绘制去极值后的数据
plt.subplot(2, 1, 2)  # 2行1列，第2个图
winsorized_data.plot(title=‘基于百分位去极值后的数据分布‘, color=‘orange‘)
plt.grid(True)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_53.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_55.png)

plt.tight_layout()
plt.show()
```

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_57.png)

通过上下对比两个子图，可以清晰地看到，处理后的数据（下图）的极端高值和低值已经被“拉回”到设定的Q1和Q3边界附近，数据范围变得集中且规范。

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_59.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_61.png)

## 关键点与注意事项 📝

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_63.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_65.png)

以下是关于本方法的一些重要说明和实际应用建议。

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_67.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_69.png)

*   **百分位的选择**：本例中使用25%和75%分位是为了演示效果明显。在实际的金融因子分析中，为了更严格地剔除极值，可能会选择更极端的百分位，例如 **2.5% 和 97.5%**（即 `low_percentile=0.025, high_percentile=0.975`）。具体阈值需要根据数据特性和分析目标进行调整。
*   **`np.clip`函数的作用**：它是实现数据裁剪的关键，其公式可以表示为：
    *   `result[i] = { q_low if series[i] < q_low; series[i] if q_low <= series[i] <= q_high; q_high if series[i] > q_high }`
*   **方法优点**：此方法不依赖于数据服从特定分布（如正态分布），仅基于数据自身的分布特点进行处理，因此适用性较广。

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_71.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_73.png)

## 总结 🏁

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_75.png)

![](img/6a9b0dbb09e52b7423dcfd169a2ba30a_77.png)

本节课中我们一起学习了**基于百分位去极值**的完整实现流程。我们掌握了如何通过计算Q1和Q3来定义数据的“正常”范围，并利用`np.clip`函数将超出此范围的极端值进行修正。这种方法是在量化策略研究中进行数据清洗和标准化的重要步骤之一，能有效提升模型的稳健性。记住，在实际应用中，灵活调整百分位阈值是关键。