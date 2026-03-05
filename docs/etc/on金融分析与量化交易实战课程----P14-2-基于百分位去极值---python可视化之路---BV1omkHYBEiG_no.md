# 金融数据分析：P14：2-基于百分位去极值实例 📊

![](img/315e2856edfb5fc19fe86a42eb434e0c_1.png)

在本节课中，我们将要学习如何使用Python，基于百分位法对金融数据进行去极值处理。这是一种常见的数据预处理技术，旨在将数据规范到合理的范围内，减少极端值对后续分析的干扰。

![](img/315e2856edfb5fc19fe86a42eb434e0c_3.png)

上一节我们介绍了数据预处理的重要性，本节中我们来看看如何具体实现基于百分位的去极值方法。

![](img/315e2856edfb5fc19fe86a42eb434e0c_5.png)

## 核心思路与函数定义

![](img/315e2856edfb5fc19fe86a42eb434e0c_7.png)

![](img/315e2856edfb5fc19fe86a42eb434e0c_9.png)

我们的目标是：给定一个数据序列，计算其第一四分位数（Q1）和第三四分位数（Q3）。然后，将所有小于Q1的值设置为Q1，将所有大于Q3的值设置为Q3。这样，数据就被“修剪”到了Q1到Q3的范围内。

以下是实现此功能的核心函数：

![](img/315e2856edfb5fc19fe86a42eb434e0c_11.png)

![](img/315e2856edfb5fc19fe86a42eb434e0c_13.png)

```python
import pandas as pd
import numpy as np

![](img/315e2856edfb5fc19fe86a42eb434e0c_15.png)

![](img/315e2856edfb5fc19fe86a42eb434e0c_17.png)

def winsorize_by_percentile(series, min_percentile=0.25, max_percentile=0.75):
    """
    基于百分位对序列进行去极值处理（缩尾处理）。
    
    参数:
    series: pandas Series，待处理的数据列。
    min_percentile: float，下限百分位，例如0.25代表第一四分位数。
    max_percentile: float，上限百分位，例如0.75代表第三四分位数。
    
    返回:
    pandas Series，处理后的数据。
    """
    # 第一步：对数据进行排序
    sorted_series = series.sort_values()
    
    # 第二步：计算指定百分位对应的值
    quantiles = sorted_series.quantile([min_percentile, max_percentile])
    min_val = quantiles.iloc[0]  # 下限值（如Q1）
    max_val = quantiles.iloc[1]  # 上限值（如Q3）
    
    # 第三步：使用np.clip将数据限制在[min_val, max_val]范围内
    # 小于min_val的值变为min_val，大于max_val的值变为max_val
    winsorized_series = np.clip(series, min_val, max_val)
    
    return winsorized_series
```

![](img/315e2856edfb5fc19fe86a42eb434e0c_19.png)

## 函数应用与效果对比

![](img/315e2856edfb5fc19fe86a42eb434e0c_21.png)

接下来，我们将这个函数应用到实际数据上，并可视化处理前后的效果，以便直观理解。

首先，加载数据并选择一列进行操作。这里我们假设数据已加载到名为 `data` 的DataFrame中。

```python
# 假设我们选择名为 ‘column_name’ 的列进行处理
column_to_process = ‘column_name’
original_data = data[column_to_process]

![](img/315e2856edfb5fc19fe86a42eb434e0c_23.png)

# 应用去极值函数
# 使用默认的25%和75%作为分位点
winsorized_data = winsorize_by_percentile(original_data)
```

为了清晰地看到处理效果，我们需要将原始数据和处理后的数据绘制在同一张图上进行对比。

以下是绘图代码：

```python
import matplotlib.pyplot as plt

![](img/315e2856edfb5fc19fe86a42eb434e0c_25.png)

# 创建图形
plt.figure(figsize=(10, 6))

# 绘制原始数据（放在上层，用半透明显示重叠部分）
plt.plot(original_data.values, label=‘原始数据’, alpha=0.7, linewidth=2)

![](img/315e2856edfb5fc19fe86a42eb434e0c_27.png)

![](img/315e2856edfb5fc19fe86a42eb434e0c_29.png)

# 绘制去极值后的数据（放在下层）
plt.plot(winsorized_data.values, label=‘去极值后数据’, alpha=0.7, linewidth=2)

![](img/315e2856edfb5fc19fe86a42eb434e0c_31.png)

# 添加图例和标题
plt.legend()
plt.title(‘基于百分位去极值效果对比’)
plt.xlabel(‘数据点索引’)
plt.ylabel(‘数值’)
plt.grid(True, alpha=0.3)
plt.show()
```

![](img/315e2856edfb5fc19fe86a42eb434e0c_33.png)

在生成的图表中，您可以观察到：
*   **原始数据**：可能包含上下波动的极端值。
*   **去极值后数据**：数据的波动范围被限制在计算出的Q1和Q3之间。原本低于Q1的“谷底”被提升到Q1的水平线，原本高于Q3的“峰值”被压低到Q3的水平线。

![](img/315e2856edfb5fc19fe86a42eb434e0c_35.png)

## 参数调整与实际应用

![](img/315e2856edfb5fc19fe86a42eb434e0c_37.png)

在上面的例子中，我们使用了 `min_percentile=0.25` 和 `max_percentile=0.75`。这意味着我们将25%的最小值和25%的最大值都视为了需要处理的极值，范围较大，主要用于演示。

![](img/315e2856edfb5fc19fe86a42eb434e0c_39.png)

在实际的金融数据分析中，例如处理因子或收益率数据时，我们通常会使用更严格的范围，以保留更多原始信息。常见的参数设置为：

![](img/315e2856edfb5fc19fe86a42eb434e0c_41.png)

以下是几种常见的百分位参数设置：
*   **较宽松（用于演示）**：`(0.25, 0.75)`，处理上下各25%的数据。
*   **常见标准**：`(0.025, 0.975)`，处理上下各2.5%的数据。
*   **更严格**：`(0.01, 0.99)`，处理上下各1%的数据。

![](img/315e2856edfb5fc19fe86a42eb434e0c_43.png)

您可以根据具体数据和模型的需要，灵活调整 `min_percentile` 和 `max_percentile` 参数。

![](img/315e2856edfb5fc19fe86a42eb434e0c_45.png)

```python
# 实际应用中更常用的参数设置
winsorized_data_strict = winsorize_by_percentile(original_data, min_percentile=0.025, max_percentile=0.975)
```

![](img/315e2856edfb5fc19fe86a42eb434e0c_47.png)

![](img/315e2856edfb5fc19fe86a42eb434e0c_49.png)

## 总结

![](img/315e2856edfb5fc19fe86a42eb434e0c_51.png)

本节课中我们一起学习了基于百分位进行去极值处理的完整流程。

![](img/315e2856edfb5fc19fe86a42eb434e0c_53.png)

![](img/315e2856edfb5fc19fe86a42eb434e0c_55.png)

我们首先定义了核心函数 `winsorize_by_percentile`，其逻辑分为三步：**数据排序**、**计算分位点**以及**使用`np.clip`进行数值截断**。随后，我们将该函数应用于示例数据，并通过可视化对比图直观展示了处理效果——数据被有效地规范到了指定的百分位范围内。最后，我们讨论了如何根据实际情况调整分位点参数，例如从演示用的`(0.25, 0.75)`调整为更符合金融数据分析实践的`(0.025, 0.975)`。

![](img/315e2856edfb5fc19fe86a42eb434e0c_57.png)

![](img/315e2856edfb5fc19fe86a42eb434e0c_59.png)

这种方法简单有效，是金融数据预处理中清洗异常值、平滑数据的常用工具之一。