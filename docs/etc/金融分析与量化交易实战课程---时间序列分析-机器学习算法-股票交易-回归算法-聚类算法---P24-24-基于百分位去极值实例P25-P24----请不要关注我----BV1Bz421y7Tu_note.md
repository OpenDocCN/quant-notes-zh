# 人生苦短，我学量化！：P24：基于百分位去极值实例

![](img/8a6793af3bd20292f0c3f8026a33ee6e_0.png)

![](img/8a6793af3bd20292f0c3f8026a33ee6e_2.png)

## 📖 概述
在本节课中，我们将学习一种在金融数据分析中常用的数据预处理技术：**基于百分位去极值**。我们将通过编写一个Python函数，将数据中超出指定百分位范围（例如，小于25%分位数或大于75%分位数）的极端值，替换为对应的边界值，从而将数据规范到一个合理的范围内。

![](img/8a6793af3bd20292f0c3f8026a33ee6e_4.png)

---

![](img/8a6793af3bd20292f0c3f8026a33ee6e_6.png)

## 🛠️ 核心步骤与代码实现

![](img/8a6793af3bd20292f0c3f8026a33ee6e_8.png)

![](img/8a6793af3bd20292f0c3f8026a33ee6e_10.png)

上一节我们介绍了去极值的概念，本节中我们来看看如何通过编写函数来实现基于百分位的去极值方法。

![](img/8a6793af3bd20292f0c3f8026a33ee6e_12.png)

首先，我们需要指定要对数据中的哪一列进行操作。

![](img/8a6793af3bd20292f0c3f8026a33ee6e_14.png)

![](img/8a6793af3bd20292f0c3f8026a33ee6e_16.png)

```python
# 假设我们有一个名为 `data` 的DataFrame
column_name = ‘某列名‘  # 请替换为实际的列名
```

![](img/8a6793af3bd20292f0c3f8026a33ee6e_18.png)

![](img/8a6793af3bd20292f0c3f8026a33ee6e_20.png)

接下来，我们将编写一个函数来完成过滤操作。这个函数将处理传入的数据序列，并根据指定的最小和最大百分位进行极值处理。

![](img/8a6793af3bd20292f0c3f8026a33ee6e_22.png)

以下是该函数的定义：

```python
import pandas as pd
import numpy as np

def filter_by_percentile(series, min_percentile=0.25, max_percentile=0.75):
    “““
    基于百分位去极值函数。
    参数:
        series: 待处理的Pandas Series数据。
        min_percentile: 最小百分位（下界），默认0.25。
        max_percentile: 最大百分位（上界），默认0.75。
    返回:
        处理后的Series数据。
    “““
    # 第一步：对数据进行排序
    sorted_series = series.sort_values()
    
    # 第二步：计算指定百分位对应的值
    q = sorted_series.quantile([min_percentile, max_percentile])
    min_value = q.iloc[0]  # 下边界值
    max_value = q.iloc[1]  # 上边界值
    
    # 第三步：使用np.clip将超出范围的值替换为边界值
    # 公式：clipped_value = max(min(x, max_value), min_value)
    filtered_series = np.clip(series, min_value, max_value)
    
    return filtered_series
```

![](img/8a6793af3bd20292f0c3f8026a33ee6e_24.png)

## 🔍 函数使用与效果对比

![](img/8a6793af3bd20292f0c3f8026a33ee6e_26.png)

代码写完后，我们来看看它的去极值效果。

以下是使用该函数并可视化结果的步骤：

![](img/8a6793af3bd20292f0c3f8026a33ee6e_28.png)

```python
# 应用函数到指定列
filtered_data = filter_by_percentile(data[column_name])

# 为了对比效果，我们绘制原始数据和处理后数据的分布图
import matplotlib.pyplot as plt

![](img/8a6793af3bd20292f0c3f8026a33ee6e_30.png)

plt.figure(figsize=(12, 5))

![](img/8a6793af3bd20292f0c3f8026a33ee6e_32.png)

![](img/8a6793af3bd20292f0c3f8026a33ee6e_34.png)

# 绘制原始数据分布
plt.subplot(1, 2, 1)
plt.hist(data[column_name], bins=50, alpha=0.7, color=‘blue‘)
plt.title(‘原始数据分布‘)
plt.xlabel(‘值‘)
plt.ylabel(‘频数‘)

![](img/8a6793af3bd20292f0c3f8026a33ee6e_36.png)

# 绘制处理后数据分布
plt.subplot(1, 2, 2)
plt.hist(filtered_data, bins=50, alpha=0.7, color=‘orange‘)
plt.title(‘去极值后数据分布‘)
plt.xlabel(‘值‘)
plt.ylabel(‘频数‘)

![](img/8a6793af3bd20292f0c3f8026a33ee6e_38.png)

plt.tight_layout()
plt.show()
```

![](img/8a6793af3bd20292f0c3f8026a33ee6e_40.png)

从生成的对比图中可以观察到，处理后数据（橙色）的分布范围被明显限制在了原始数据（蓝色）的中间区域，两端的极端值被“修剪”掉了。

![](img/8a6793af3bd20292f0c3f8026a33ee6e_42.png)

## 💡 注意事项与实际应用

![](img/8a6793af3bd20292f0c3f8026a33ee6e_44.png)

![](img/8a6793af3bd20292f0c3f8026a33ee6e_46.png)

在实际的量化分析或因子处理中，我们通常不会使用像0.25和0.75这样宽泛的边界，因为这会将过多数据视为异常值。

![](img/8a6793af3bd20292f0c3f8026a33ee6e_48.png)

以下是更常用的参数设置建议：

![](img/8a6793af3bd20292f0c3f8026a33ee6e_50.png)

![](img/8a6793af3bd20292f0c3f8026a33ee6e_52.png)

![](img/8a6793af3bd20292f0c3f8026a33ee6e_54.png)

*   **常用分位点**：更常见的做法是使用 **0.025** 和 **0.975** 作为上下边界，即只处理分布两端各2.5%的极端值。
*   **参数灵活性**：本示例为了展示更明显的效果，使用了较大的范围。在实际应用中，你可以根据数据特性和分析目标灵活调整 `min_percentile` 和 `max_percentile` 参数。

![](img/8a6793af3bd20292f0c3f8026a33ee6e_56.png)

## 📝 总结
本节课中我们一起学习了**基于百分位去极值**的完整流程。

![](img/8a6793af3bd20292f0c3f8026a33ee6e_58.png)

![](img/8a6793af3bd20292f0c3f8026a33ee6e_60.png)

1.  **核心思想**：通过计算数据的特定分位数（如Q1和Q3）作为边界，将超出边界的数据值替换为边界值本身。
2.  **实现方法**：我们使用Pandas的 `.quantile()` 方法计算分位数值，并利用NumPy的 `np.clip()` 函数高效地进行数值裁剪。
3.  **应用价值**：这种方法能有效平滑数据，减少极端异常值对后续统计分析或机器学习模型造成的负面影响，是金融数据预处理中的一项基础且重要的技术。

![](img/8a6793af3bd20292f0c3f8026a33ee6e_62.png)

![](img/8a6793af3bd20292f0c3f8026a33ee6e_64.png)

你可以将本节课编写的函数作为一个工具，应用到自己的数据分析项目中，通过调整百分位参数来适应不同的数据场景。