# Python金融分析与量化交易实战教程：P27：02-2-基于百分位去极值实例 📊

![](img/1a7409c4b8ff8fa234745291ddf26a6e_1.png)

![](img/1a7409c4b8ff8fa234745291ddf26a6e_3.png)

在本节课中，我们将学习一种在金融数据分析中常用的数据预处理技术——基于百分位法去除极值。我们将通过一个具体的实例，编写一个函数，将数据规范到由第一四分位数（Q1）和第三四分位数（Q3）定义的范围内。

![](img/1a7409c4b8ff8fa234745291ddf26a6e_5.png)

## 概述
数据中的极值（或称异常值）可能会对后续的分析和建模产生不良影响。基于百分位去极值是一种稳健的方法，其核心思想是：将所有小于下界（如Q1）的值设置为下界，将所有大于上界（如Q3）的值设置为上界，从而将数据限制在一个合理的范围内。

![](img/1a7409c4b8ff8fa234745291ddf26a6e_7.png)

![](img/1a7409c4b8ff8fa234745291ddf26a6e_9.png)

## 实现步骤详解

![](img/1a7409c4b8ff8fa234745291ddf26a6e_11.png)

上一节我们介绍了去极值的概念，本节中我们来看看如何用代码实现基于百分位的去极值方法。

![](img/1a7409c4b8ff8fa234745291ddf26a6e_13.png)

![](img/1a7409c4b8ff8fa234745291ddf26a6e_15.png)

![](img/1a7409c4b8ff8fa234745291ddf26a6e_17.png)

### 1. 选择目标数据列
首先，我们需要指定要对数据框（DataFrame）中的哪一列进行操作。以下代码演示了如何选择一列数据。

![](img/1a7409c4b8ff8fa234745291ddf26a6e_19.png)

```python
# 假设 `data` 是一个 pandas DataFrame
column_name = ‘D‘  # 选择名为‘D‘的列
selected_series = data[column_name]
```

### 2. 编写去极值函数
接下来，我们需要编写一个函数来完成核心的去极值操作。这个函数将接收一个数据序列（Series）以及我们想要保留的数据范围（最小百分位和最大百分位）。

以下是该函数的完整代码：

![](img/1a7409c4b8ff8fa234745291ddf26a6e_21.png)

```python
import pandas as pd
import numpy as np

def winsorize_by_percentile(series, low_percentile=0.25, high_percentile=0.75):
    “““
    基于百分位法对序列进行去极值处理。
    参数:
        series: pandas Series，待处理的数据序列。
        low_percentile: float，下界百分位（例如0.25代表Q1）。
        high_percentile: float，上界百分位（例如0.75代表Q3）。
    返回:
        pandas Series，处理后的数据序列。
    “““
    # 第一步：对数据进行排序
    sorted_series = series.sort_values()
    
    # 第二步：计算指定百分位对应的实际数值
    # 使用 quantile 函数计算分位数
    percentile_values = sorted_series.quantile([low_percentile, high_percentile])
    lower_bound = percentile_values.iloc[0]  # 下界值（如Q1）
    upper_bound = percentile_values.iloc[1]  # 上界值（如Q3）
    
    # 第三步：使用 numpy 的 clip 函数将数据规范到 [lower_bound, upper_bound] 区间
    # np.clip(array, min, max) 将 array 中小于 min 的值设为 min，大于 max 的值设为 max
    winsorized_series = np.clip(series, lower_bound, upper_bound)
    
    return winsorized_series
```

![](img/1a7409c4b8ff8fa234745291ddf26a6e_23.png)

**代码解析：**
*   `series.sort_values()`: 将数据从小到大排序，这是准确计算百分位的前提。
*   `series.quantile([low_percentile, high_percentile])`: Pandas提供的函数，直接计算并返回指定百分位对应的数值。
*   `np.clip(series, lower_bound, upper_bound)`: NumPy提供的函数，是完成“小于下界的设为下界，大于上界的设为上界”这一操作最简洁高效的方式。

### 3. 应用函数并可视化结果
函数编写完成后，我们可以将其应用到选定的数据列上，并通过对比图表来直观地观察去极值的效果。

![](img/1a7409c4b8ff8fa234745291ddf26a6e_25.png)

![](img/1a7409c4b8ff8fa234745291ddf26a6e_27.png)

以下是应用函数和绘制对比图的代码：

![](img/1a7409c4b8ff8fa234745291ddf26a6e_29.png)

```python
# 应用去极值函数
# 这里使用默认的 0.25 和 0.75 作为分位点，以便演示效果更明显
winsorized_data = winsorize_by_percentile(selected_series)

![](img/1a7409c4b8ff8fa234745291ddf26a6e_31.png)

# 绘制原始数据与去极值后数据的对比图
import matplotlib.pyplot as plt

![](img/1a7409c4b8ff8fa234745291ddf26a6e_33.png)

![](img/1a7409c4b8ff8fa234745291ddf26a6e_35.png)

plt.figure(figsize=(10, 6))

![](img/1a7409c4b8ff8fa234745291ddf26a6e_37.png)

# 绘制原始数据（红色）
plt.plot(selected_series.values, label=‘Original Data‘, color=‘red‘, alpha=0.7)
# 绘制去极值后的数据（蓝色）
plt.plot(winsorized_data.values, label=‘Winsorized Data‘, color=‘blue‘, alpha=0.7)

![](img/1a7409c4b8ff8fa234745291ddf26a6e_39.png)

plt.title(‘Comparison: Original Data vs. Winsorized Data‘)
plt.xlabel(‘Index‘)
plt.ylabel(‘Value‘)
plt.legend()
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

![](img/1a7409c4b8ff8fa234745291ddf26a6e_41.png)

![](img/1a7409c4b8ff8fa234745291ddf26a6e_43.png)

**图表说明：** 生成的图表中，红色线代表原始数据，蓝色线代表处理后的数据。可以清晰地看到，原始数据中过高和过低的“尖峰”被“削平”了，蓝色数据被限制在了一个更窄、更稳定的范围内。

![](img/1a7409c4b8ff8fa234745291ddf26a6e_45.png)

![](img/1a7409c4b8ff8fa234745291ddf26a6e_47.png)

![](img/1a7409c4b8ff8fa234745291ddf26a6e_49.png)

## 重要补充说明
在理论介绍和代码实现后，关于参数选择还需要注意以下几点：

![](img/1a7409c4b8ff8fa234745291ddf26a6e_51.png)

![](img/1a7409c4b8ff8fa234745291ddf26a6e_53.png)

以下是关于百分位参数选择的注意事项：
*   **演示与实战的差异**：为了在示例中让效果更明显，我们使用了`0.25`和`0.75`作为分位点。这意味着我们将25%的最小值和25%的最大值都视为了极值并进行处理，范围较大。
*   **实际应用中的参数**：在真实的金融因子分析或数据处理中，通常会使用更严格的范围，例如`0.025`和`0.975`。这意味着只将分布两端各2.5%的数据视为极值，这样能在去除极端异常值的同时，保留更多有效数据的信息。
*   **参数灵活性**：我们的`winsorize_by_percentile`函数设计是灵活的，你可以通过传入不同的`low_percentile`和`high_percentile`参数来适应不同严格度的去极值需求。

![](img/1a7409c4b8ff8fa234745291ddf26a6e_55.png)

![](img/1a7409c4b8ff8fa234745291ddf26a6e_57.png)

## 总结
本节课中我们一起学习了基于百分位法去除数据极值的完整流程。我们首先理解了该方法的核心思想——利用分位数设定数据边界并进行截断。然后，我们逐步实现了三个关键步骤：**1）选择数据列；2）编写一个利用`np.clip`和`series.quantile`的高效去极值函数；3）应用函数并通过可视化对比效果**。最后，我们讨论了实际应用中如何合理选择百分位参数。掌握这一方法能有效提升数据质量，为后续的金融分析与建模打下坚实基础。