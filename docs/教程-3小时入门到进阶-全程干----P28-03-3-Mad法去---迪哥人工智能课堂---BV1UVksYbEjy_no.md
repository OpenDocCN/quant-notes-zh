# Python金融分析与量化交易实战教程：P28：03-3-MAD法去极值演示

![](img/c2742456ceb8454ae109cf18f6f9acc2_0.png)

![](img/c2742456ceb8454ae109cf18f6f9acc2_2.png)

在本节课中，我们将要学习第二种数据去极值方法——MAD法。我们将详细解释其原理，并通过代码演示如何实现。

## 概述

![](img/c2742456ceb8454ae109cf18f6f9acc2_4.png)

上一节我们介绍了百分位法去极值，本节中我们来看看另一种基于中位数绝对偏差的方法，即MAD法。这种方法通过计算数据的中位数和绝对偏差来设定合理的上下界，从而识别并处理异常值。

## MAD法原理

MAD法的核心思想是使用数据的中位数作为中心点，并计算每个数据点与中位数偏差的绝对值的中位数（即MAD值），以此来确定数据的正常范围。

![](img/c2742456ceb8454ae109cf18f6f9acc2_6.png)

以下是计算步骤：
1.  计算原始数据序列的中位数 `median`。
2.  计算每个数据点与中位数偏差的绝对值，得到一个新的序列 `abs_deviation`。
    *   **公式**：`abs_deviation = |data - median|`
3.  计算这个新序列 `abs_deviation` 的中位数，这个值就是MAD值。
    *   **公式**：`MAD = median(|data - median|)`
4.  设定正常值的上下界。通常使用一个常数 `N`（常取1.4826）乘以MAD值来扩展范围。
    *   **上界公式**：`upper_bound = median + N * MAD`
    *   **下界公式**：`lower_bound = median - N * MAD`
5.  将超出上下界的数据视为极值，并进行处理（如缩尾或截尾）。

![](img/c2742456ceb8454ae109cf18f6f9acc2_8.png)

## 代码实现

![](img/c2742456ceb8454ae109cf18f6f9acc2_10.png)

![](img/c2742456ceb8454ae109cf18f6f9acc2_12.png)

以下是MAD法去极值的Python函数实现：

```python
import pandas as pd
import numpy as np

![](img/c2742456ceb8454ae109cf18f6f9acc2_14.png)

![](img/c2742456ceb8454ae109cf18f6f9acc2_16.png)

def filter_extreme_mad(series, n=1.4826):
    """
    使用MAD法对序列进行去极值处理。
    
    参数:
    series (pd.Series): 待处理的数据序列。
    n (float): 用于计算边界的倍数，默认为1.4826。
    
    返回:
    pd.Series: 处理后的数据序列。
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)
    
    # 2. 计算每个数据点与中位数偏差的绝对值
    abs_deviation = (series - median).abs()
    
    # 3. 计算绝对偏差序列的中位数，即MAD值
    mad = abs_deviation.quantile(0.5)
    
    # 4. 计算上下界
    upper_bound = median + n * mad
    lower_bound = median - n * mad
    
    # 5. 对超出边界的数据进行缩尾处理
    filtered_series = series.clip(lower=lower_bound, upper=upper_bound)
    
    return filtered_series
```

![](img/c2742456ceb8454ae109cf18f6f9acc2_18.png)

![](img/c2742456ceb8454ae109cf18f6f9acc2_20.png)

## 应用演示

![](img/c2742456ceb8454ae109cf18f6f9acc2_22.png)

![](img/c2742456ceb8454ae109cf18f6f9acc2_24.png)

现在，我们使用这个函数对一个示例数据集进行处理，并可视化结果。

```python
# 假设我们有一个包含极值的数据序列
np.random.seed(42)
data = pd.Series(np.random.randn(100))  # 100个正态分布随机数
data.iloc[0] = 10  # 人为添加一个极大值
data.iloc[1] = -10 # 人为添加一个极小值

# 应用MAD法去极值
filtered_data = filter_extreme_mad(data)

![](img/c2742456ceb8454ae109cf18f6f9acc2_26.png)

# 可视化对比
import matplotlib.pyplot as plt

![](img/c2742456ceb8454ae109cf18f6f9acc2_28.png)

![](img/c2742456ceb8454ae109cf18f6f9acc2_30.png)

plt.figure(figsize=(10, 6))
plt.plot(data.values, label='原始数据', alpha=0.7, marker='o')
plt.plot(filtered_data.values, label='MAD法处理后', alpha=0.7, marker='x')
plt.axhline(y=filtered_data.max(), color='r', linestyle='--', alpha=0.5, label='上界')
plt.axhline(y=filtered_data.min(), color='g', linestyle='--', alpha=0.5, label='下界')
plt.legend()
plt.title('MAD法去极值效果对比')
plt.xlabel('数据索引')
plt.ylabel('数值')
plt.show()
```

执行以上代码后，您将看到图表。图中原始数据的两个极端点（10和-10）被拉回到了由红色和绿色虚线表示的上下界之内，而大部分正常数据保持不变。这就是MAD法去极值的效果。

![](img/c2742456ceb8454ae109cf18f6f9acc2_32.png)

![](img/c2742456ceb8454ae109cf18f6f9acc2_34.png)

## 总结

![](img/c2742456ceb8454ae109cf18f6f9acc2_36.png)

![](img/c2742456ceb8454ae109cf18f6f9acc2_38.png)

本节课中我们一起学习了MAD法去极值。我们了解了其通过计算数据的中位数和绝对偏差中位数来定义数据正常范围的原理，并动手实现了一个完整的处理函数。与百分位法相比，MAD法对极端值不那么敏感，因为它基于稳健的中位数统计量，在处理有偏分布或含有大量异常值的数据时可能更具优势。