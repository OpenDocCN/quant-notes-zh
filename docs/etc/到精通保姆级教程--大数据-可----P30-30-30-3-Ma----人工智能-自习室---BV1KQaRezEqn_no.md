# Python金融量化交易：30：MAD法去极值演示 📊

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_0.png)

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_2.png)

在本节课中，我们将学习去极值处理的第二种方法——MAD法。我们将了解其核心概念、计算步骤，并通过代码演示如何实现它。

## 概述

上一节我们介绍了百分位法去极值，本节中我们来看看另一种常用的方法：MAD法。MAD是“Median Absolute Deviation”（中位数绝对偏差）的缩写。这种方法通过数据的中位数和偏差来定义正常值的范围，从而识别并处理极端值。

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_4.png)

## MAD法核心概念与公式

MAD法的核心思想是：**先计算数据序列的中位数，然后计算每个数据点与中位数之差的绝对值，再求这些绝对值的中位数，最后利用这个“中位数绝对偏差”来设定正常值的上下边界。**

以下是计算步骤的公式化描述：

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_6.png)

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_8.png)

1.  计算原始数据序列 `X` 的中位数 `median_X`。
    *   `median_X = median(X)`
2.  计算每个数据点与中位数的绝对偏差。
    *   `abs_deviation = |X - median_X|`
3.  计算这些绝对偏差的中位数，即MAD值。
    *   `MAD = median(abs_deviation)`
4.  设定正常值的上下边界。通常使用一个缩放系数 `n`（常取 1.4826）与MAD相乘。
    *   `upper_bound = median_X + n * MAD`
    *   `lower_bound = median_X - n * MAD`
5.  将超出边界 `[lower_bound, upper_bound]` 的数据点视为极值，并进行处理（如缩尾或截尾）。

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_10.png)

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_12.png)

## 代码实现

以下是MAD去极值函数的Python实现代码：

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_14.png)

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_16.png)

```python
import pandas as pd
import numpy as np

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_18.png)

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_20.png)

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_22.png)

def filter_extreme_MAD(series, n):
    """
    使用MAD法对序列进行去极值处理。
    
    参数:
    series -- 待处理的数据序列 (pandas Series)
    n -- 用于计算边界的MAD倍数 (float)
    
    返回:
    处理后的数据序列 (pandas Series)
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)
    
    # 2. 计算每个数据点与中位数的绝对偏差，并求其中位数 (MAD)
    mad = (series - median).abs().quantile(0.5)
    
    # 3. 计算上下边界
    upper_bound = median + n * mad
    lower_bound = median - n * mad
    
    # 4. 对超出边界的数据进行缩尾处理
    filtered_series = series.clip(lower=lower_bound, upper=upper_bound)
    
    return filtered_series
```

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_24.png)

## 函数使用与结果演示

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_26.png)

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_28.png)

现在，我们使用这个函数来处理一组示例数据，并可视化处理前后的对比。

以下是使用示例：

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_30.png)

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_32.png)

```python
# 假设我们有一组包含极值的数据
data = pd.Series([1, 2, 3, 4, 5, 6, 7, 8, 9, 100])  # 100是一个明显的极值

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_34.png)

# 应用MAD法去极值，n取常用值1.4826
n_value = 1.4826
filtered_data = filter_extreme_MAD(data, n_value)

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_36.png)

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_38.png)

print("原始数据：")
print(data.values)
print("\nMAD法处理后的数据：")
print(filtered_data.values)

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_40.png)

# 可视化对比（需要matplotlib库）
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 4))
plt.subplot(1, 2, 1)
plt.plot(data.values, 'o-', label='原始数据')
plt.title('原始数据')
plt.legend()

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_42.png)

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_44.png)

plt.subplot(1, 2, 2)
plt.plot(filtered_data.values, 'o-', color='orange', label='MAD处理后数据')
plt.title('MAD法去极值后')
plt.legend()
plt.tight_layout()
plt.show()
```

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_46.png)

执行上述代码后，你会发现原始数据中的极值（如100）会被拉回到由MAD法计算出的合理上界附近，而大部分正常数据保持不变。通过图表可以清晰地看到处理效果：蓝色线代表原始数据（包含一个高点），橙色线代表处理后的数据，极端高点已被修正。

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_48.png)

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_50.png)

## 总结

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_52.png)

![](img/27ca645d4a8aa5db48e7aaf081dd9f4d_54.png)

本节课中我们一起学习了MAD去极值法。我们首先了解了其基于中位数和绝对偏差的核心原理，然后逐步推导了其计算公式。接着，我们编写了完整的Python函数来实现这一算法，并通过示例数据和图表演示了它的实际应用效果。MAD法对异常值不敏感，比基于标准差的方法更稳健，是金融数据预处理中一个非常实用的工具。