# 量化交易实战：P29：基于百分位去极值实例 📊

![](img/8ca34f8d3781a2b710ac195c95274ad4_1.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_3.png)

在本节课中，我们将学习如何使用Python和Pandas库，基于百分位数（如Q1和Q3）来去除数据集中的极端值。这是一种常见的数据预处理技术，旨在将数据规范到一个合理的范围内，以提高后续分析的稳定性。

![](img/8ca34f8d3781a2b710ac195c95274ad4_5.png)

上一节我们介绍了数据清洗的基本概念，本节中我们来看看如何具体实现基于百分位的去极值操作。

![](img/8ca34f8d3781a2b710ac195c95274ad4_7.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_9.png)

## 核心步骤与代码实现

![](img/8ca34f8d3781a2b710ac195c95274ad4_11.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_13.png)

以下是实现基于百分位去极值功能的核心步骤。

![](img/8ca34f8d3781a2b710ac195c95274ad4_15.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_17.png)

首先，我们需要定义一个函数。这个函数将接收一个数据序列（Pandas Series），并根据指定的下限和上限百分位（例如25%和75%）来“修剪”数据。小于下限的值将被设置为下限值，大于上限的值将被设置为上限值。

![](img/8ca34f8d3781a2b710ac195c95274ad4_19.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_21.png)

```python
import pandas as pd
import numpy as np

def winsorize_by_percentile(series, low_percentile=0.25, high_percentile=0.75):
    """
    基于百分位对序列进行去极值处理（缩尾处理）。
    参数:
        series: Pandas Series，待处理的数据列。
        low_percentile: 下限百分位，例如0.25代表Q1。
        high_percentile: 上限百分位，例如0.75代表Q3。
    返回:
        处理后的Pandas Series。
    """
    # 1. 对数据进行排序
    sorted_series = series.sort_values()

    # 2. 计算指定百分位对应的具体数值
    # quantile函数可以直接计算分位数
    q_values = sorted_series.quantile([low_percentile, high_percentile])
    low_bound = q_values.iloc[0]  # 下限值
    high_bound = q_values.iloc[1] # 上限值

    # 3. 使用np.clip将数据规范到[low_bound, high_bound]区间
    # 小于low_bound的值变为low_bound，大于high_bound的值变为high_bound
    winsorized_series = np.clip(series, low_bound, high_bound)

    return winsorized_series
```

![](img/8ca34f8d3781a2b710ac195c95274ad4_23.png)

## 函数应用与效果对比

![](img/8ca34f8d3781a2b710ac195c95274ad4_25.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_27.png)

代码写完后，接下来我们将其应用于实际数据，并观察去极值的效果。

![](img/8ca34f8d3781a2b710ac195c95274ad4_29.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_31.png)

假设我们有一个名为`data`的DataFrame，其中包含一列名为`‘target_column’`的数据。以下是应用函数并可视化对比结果的流程。

![](img/8ca34f8d3781a2b710ac195c95274ad4_33.png)

```python
# 假设 data 是一个已加载的Pandas DataFrame
# 选择要处理的列
target_series = data['target_column']

![](img/8ca34f8d3781a2b710ac195c95274ad4_35.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_37.png)

# 应用去极值函数
# 使用默认的25%和75%分位点
winsorized_series = winsorize_by_percentile(target_series)

# 可视化对比原始数据与处理后的数据
import matplotlib.pyplot as plt

![](img/8ca34f8d3781a2b710ac195c95274ad4_39.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_41.png)

plt.figure(figsize=(12, 6))

![](img/8ca34f8d3781a2b710ac195c95274ad4_43.png)

# 绘制原始数据
plt.subplot(2, 1, 1)
target_series.plot(title='原始数据')
plt.grid(True)

![](img/8ca34f8d3781a2b710ac195c95274ad4_45.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_47.png)

# 绘制去极值后的数据
plt.subplot(2, 1, 2)
winsorized_series.plot(title='基于百分位去极值后的数据', color='orange')
plt.grid(True)

![](img/8ca34f8d3781a2b710ac195c95274ad4_49.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_51.png)

plt.tight_layout()
plt.show()
```

![](img/8ca34f8d3781a2b710ac195c95274ad4_53.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_55.png)

从生成的图表可以清晰看到，处理后的数据范围被限制在了由Q1和Q3定义的区间内。原先低于Q1的极低值被提升至Q1，高于Q3的极高值被降低至Q3，从而实现了数据的规范化。

![](img/8ca34f8d3781a2b710ac195c95274ad4_57.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_59.png)

## 参数调整与实际应用

![](img/8ca34f8d3781a2b710ac195c95274ad4_61.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_63.png)

在实际的量化因子分析中，我们通常不会使用25%和75%这样宽泛的分位点，因为这会将过多正常数据判定为异常值。

![](img/8ca34f8d3781a2b710ac195c95274ad4_65.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_67.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_69.png)

以下是更常用的参数设置建议。

![](img/8ca34f8d3781a2b710ac195c95274ad4_71.png)

*   **常用分位点**：更常见的做法是使用如 **2.5%** 和 **97.5%** 的分位点，即只对分布两端各2.5%的极端值进行处理。
*   **参数灵活性**：我们定义的函数`winsorize_by_percentile`具有灵活性，可以通过传入`low_percentile=0.025`和`high_percentile=0.975`来应用更严格的标准。
*   **领域依赖性**：最佳分位点阈值需要根据具体因子或数据的分布特性进行回测和调整，没有统一标准。

![](img/8ca34f8d3781a2b710ac195c95274ad4_73.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_75.png)

![](img/8ca34f8d3781a2b710ac195c95274ad4_77.png)

本节课中我们一起学习了基于百分位数进行数据去极值（缩尾处理）的完整方法。我们掌握了其核心思想：**`np.clip(series, low_bound, high_bound)`**，并实现了一个灵活可复用的函数。通过可视化对比，我们直观地看到了该方法如何将数据规范到指定范围。最后，我们讨论了在实际金融因子分析中更常用的参数设置（如2.5%/97.5%），为后续的量化建模打下了坚实的数据预处理基础。