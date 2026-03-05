# Python金融分析与量化交易实战教程：P27：02-2-基于百分位去极值实例 📊

![](img/955ebe1f41ea96b5e2283bb664ac6467_1.png)

在本节课中，我们将要学习如何基于百分位法对金融数据进行去极值处理。这是一种常见的数据预处理技术，旨在将数据规范到合理的范围内，减少极端值对后续分析的影响。

![](img/955ebe1f41ea96b5e2283bb664ac6467_3.png)

上一节我们介绍了去极值的概念，本节中我们来看看如何通过编程实现基于百分位的去极值方法。

![](img/955ebe1f41ea96b5e2283bb664ac6467_5.png)

## 核心思路与函数定义 🎯

![](img/955ebe1f41ea96b5e2283bb664ac6467_7.png)

![](img/955ebe1f41ea96b5e2283bb664ac6467_9.png)

我们的目标是：给定一个数据序列，计算其第一四分位数（Q1）和第三四分位数（Q3）。将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3，从而将数据限制在[Q1, Q3]的范围内。

以下是实现此功能的核心步骤，我们将定义一个函数来完成：

![](img/955ebe1f41ea96b5e2283bb664ac6467_11.png)

![](img/955ebe1f41ea96b5e2283bb664ac6467_13.png)

```python
def percentile_winsorize(series, min_percentile=0.25, max_percentile=0.75):
    """
    基于百分位进行去极值（缩尾）处理。
    参数:
        series: 输入的Pandas Series数据序列。
        min_percentile: 下分位数，默认0.25（Q1）。
        max_percentile: 上分位数，默认0.75（Q3）。
    返回:
        处理后的Pandas Series序列。
    """
    # 1. 对数据进行排序
    sorted_series = series.sort_values()

    # 2. 计算指定分位数的值
    # 使用quantile函数计算Q1和Q3
    q_values = sorted_series.quantile([min_percentile, max_percentile])
    min_val = q_values.iloc[0]  # 对应min_percentile的值
    max_val = q_values.iloc[1]  # 对应max_percentile的值

    # 3. 使用clip函数将数据限制在[min_val, max_val]范围内
    # 小于min_val的值变为min_val，大于max_val的值变为max_val
    winsorized_series = series.clip(lower=min_val, upper=max_val)

    return winsorized_series
```

![](img/955ebe1f41ea96b5e2283bb664ac6467_15.png)

![](img/955ebe1f41ea96b5e2283bb664ac6467_17.png)

## 代码执行与效果验证 🔧

![](img/955ebe1f41ea96b5e2283bb664ac6467_19.png)

定义好函数后，我们需要将其应用于实际数据并验证效果。

首先，从数据集中选择一列进行操作。这里我们以列`‘D’`为例。

```python
# 假设`data`是一个Pandas DataFrame
column_to_process = data['D']

# 应用去极值函数
processed_column = percentile_winsorize(column_to_process, min_percentile=0.25, max_percentile=0.75)
```

为了直观地对比处理前后的数据分布，我们可以绘制图表。

以下是绘制原始数据与处理后数据对比图的代码：

```python
import matplotlib.pyplot as plt

![](img/955ebe1f41ea96b5e2283bb664ac6467_21.png)

# 创建一个图形，包含上下两个子图
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(10, 8))

# 在上方子图绘制原始数据
ax1.plot(column_to_process.values, label='原始数据', color='blue', alpha=0.7)
ax1.set_title('原始数据分布')
ax1.legend()

![](img/955ebe1f41ea96b5e2283bb664ac6467_23.png)

![](img/955ebe1f41ea96b5e2283bb664ac6467_25.png)

# 在下方子图绘制处理后的数据
ax2.plot(processed_column.values, label='去极值后数据', color='orange', alpha=0.7)
ax2.set_title('基于百分位去极值后数据分布')
ax2.legend()

![](img/955ebe1f41ea96b5e2283bb664ac6467_27.png)

plt.tight_layout()
plt.show()
```

通过对比上下两个图表，可以清晰地看到，处理后的数据（下方橙色图）的波动范围被限制在了原始数据（上方蓝色图）的Q1和Q3之间。原本低于Q1的极低值被提升至Q1，高于Q3的极高值被降低至Q3。

![](img/955ebe1f41ea96b5e2283bb664ac6467_29.png)

## 参数调整与实际应用说明 💡

![](img/955ebe1f41ea96b5e2283bb664ac6467_31.png)

在演示中，我们使用了`min_percentile=0.25`和`max_percentile=0.75`，这使得处理范围较宽，便于展示效果。

![](img/955ebe1f41ea96b5e2283bb664ac6467_33.png)

![](img/955ebe1f41ea96b5e2283bb664ac6467_35.png)

但在实际的金融因子分析或量化策略中，通常会使用更严格的范围来定义“异常值”。常见的参数设置是：

*   **`min_percentile=0.025`**
*   **`max_percentile=0.975`**

![](img/955ebe1f41ea96b5e2283bb664ac6467_37.png)

![](img/955ebe1f41ea96b5e2283bb664ac6467_39.png)

这表示只保留中间95%的数据，将两端各2.5%的数据视为极值并进行处理。这样的设置更为常用，能更有效地过滤掉真正的异常点，同时保留大部分有效数据。

![](img/955ebe1f41ea96b5e2283bb664ac6467_41.png)

你可以根据具体数据和业务需求，灵活调整这两个百分位参数。

![](img/955ebe1f41ea96b5e2283bb664ac6467_43.png)

![](img/955ebe1f41ea96b5e2283bb664ac6467_45.png)

## 总结 📝

![](img/955ebe1f41ea96b5e2283bb664ac6467_47.png)

本节课中我们一起学习了基于百分位进行去极值的完整流程。

![](img/955ebe1f41ea96b5e2283bb664ac6467_49.png)

![](img/955ebe1f41ea96b5e2283bb664ac6467_51.png)

1.  **理解原理**：通过计算数据的**第一四分位数（Q1）**和**第三四分位数（Q3）**，确定数据的正常范围。
2.  **实现函数**：定义`percentile_winsorize`函数，利用Pandas的`sort_values`、`quantile`和`clip`方法，高效地完成排序、分位数计算和数值截断。
3.  **应用与验证**：将函数应用于具体数据列，并通过可视化图表对比处理前后的差异，直观验证去极值效果。
4.  **参数灵活调整**：了解到在实际应用中，可根据需要调整分位数参数（如使用2.5%和97.5%）来更精确地控制极值的判定标准。

![](img/955ebe1f41ea96b5e2283bb664ac6467_53.png)

![](img/955ebe1f41ea96b5e2283bb664ac6467_55.png)

这种方法简单有效，是金融数据预处理中标准化、消除极端值影响的常用手段之一。