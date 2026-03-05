# Python金融分析量化交易：3：MAD法去极值演示

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_0.png)

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_2.png)

在本节课中，我们将要学习第二种数据去极值的方法——MAD法。我们将从原理公式入手，理解其计算逻辑，然后通过Python代码演示如何实现该方法，并观察其处理效果。

## 概述

上一节我们介绍了百分位法去极值，本节中我们来看看另一种常用的方法：MAD法。MAD是“Median Absolute Deviation”（中位数绝对偏差）的缩写。虽然其公式看起来比百分位法稍复杂，但计算过程同样清晰明了。它的核心思想是利用数据的中位数和偏差来设定合理的上下边界，从而识别并处理极端值。

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_4.png)

## MAD法原理

MAD法的目标是确定一个数据的正常范围，将超出此范围的值视为极端值。其计算分为两个主要步骤。

首先，计算序列的**中位数绝对偏差（MAD值）**。给定一个数据序列，例如 `[1, 2, 3, 4, 5, 6, 7]`：
1.  计算该序列的中位数。假设中位数 `median = 4`。
2.  计算序列中每个数值与中位数的**绝对差值**，形成一个新的序列。例如，对于数值1：`|1 - 4| = 3`。
3.  对这个由绝对差值构成的新序列，再次求**中位数**。这个结果就是MAD值。

公式可以表示为：
**MAD = median(|X_i - median(X)|)**

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_6.png)

其次，利用计算出的MAD值设定**上下边界**。通常，我们会设定：
*   **上界 = median(X) + N * MAD**
*   **下界 = median(X) - N * MAD**

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_8.png)

其中，`N` 是一个常数系数。在统计学中，为了使MAD法与标准差估计保持一致，常取 `N = 1.4826`。任何超出 `[下界, 上界]` 范围的数据点，都将被视为极端值并进行处理（如缩尾或截尾）。

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_10.png)

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_12.png)

## Python代码实现

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_14.png)

理解了原理后，我们来看看如何用Python代码实现MAD去极值函数。以下是实现步骤：

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_16.png)

首先，导入必要的库并准备示例数据。

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_18.png)

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_20.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_22.png)

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_24.png)

# 生成示例数据，包含一些极端值
np.random.seed(42)
data = np.random.randn(100)  # 100个正态分布数据
data[0] = 10  # 加入一个大的极端值
data[1] = -8  # 加入一个小的极端值
series = pd.Series(data)
```

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_26.png)

接下来，我们定义MAD去极值函数。该函数接收一个数据序列和一个倍数N作为参数。

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_28.png)

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_30.png)

```python
def filter_extreme_mad(series, n=5):
    """
    使用MAD法对序列进行去极值处理。
    参数:
        series (pd.Series): 待处理的数据序列。
        n (float): 用于计算边界的倍数，默认为5。
    返回:
        pd.Series: 处理后的序列。
        float: 下界。
        float: 上界。
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)
    
    # 2. 计算中位数绝对偏差 (MAD)
    # 先计算每个数据点与中位数的绝对偏差
    abs_deviation = (series - median).abs()
    # 再计算这些绝对偏差的中位数，即为MAD值
    mad = abs_deviation.quantile(0.5)
    
    # 3. 计算上下边界
    high_bound = median + n * mad
    low_bound = median - n * mad
    
    # 4. 对序列进行缩尾处理：将超过边界的值拉回到边界上
    filtered_series = series.clip(lower=low_bound, upper=high_bound)
    
    return filtered_series, low_bound, high_bound
```

现在，我们可以使用这个函数来处理我们的示例数据，并可视化处理前后的对比。

```python
# 应用MAD去极值函数，使用常见的系数1.4826
filtered_data_mad, low_bound_mad, high_bound_mad = filter_extreme_mad(series, n=1.4826)

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_32.png)

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_34.png)

# 创建对比图表
plt.figure(figsize=(12, 6))

# 绘制原始数据
plt.subplot(1, 2, 1)
plt.plot(series.values, 'o', alpha=0.7, label='原始数据')
plt.axhline(y=high_bound_mad, color='r', linestyle='--', label=f'上界: {high_bound_mad:.2f}')
plt.axhline(y=low_bound_mad, color='g', linestyle='--', label=f'下界: {low_bound_mad:.2f}')
plt.axhline(y=series.quantile(0.5), color='y', linestyle='-', label='中位数', alpha=0.5)
plt.title('原始数据与MAD法边界')
plt.legend()
plt.grid(True)

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_36.png)

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_38.png)

# 绘制处理后的数据
plt.subplot(1, 2, 2)
plt.plot(filtered_data_mad.values, 'o', alpha=0.7, color='orange', label='MAD法处理后数据')
plt.axhline(y=high_bound_mad, color='r', linestyle='--', label=f'上界: {high_bound_mad:.2f}')
plt.axhline(y=low_bound_mad, color='g', linestyle='--', label=f'下界: {low_bound_mad:.2f}')
plt.axhline(y=filtered_data_mad.quantile(0.5), color='y', linestyle='-', label='中位数', alpha=0.5)
plt.title('MAD法去极值后数据')
plt.legend()
plt.grid(True)

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_40.png)

plt.tight_layout()
plt.show()

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_42.png)

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_44.png)

# 打印边界信息
print(f"计算得到的下界: {low_bound_mad:.4f}")
print(f"计算得到的上界: {high_bound_mad:.4f}")
print(f"原始数据中超出下界的值有: {(series < low_bound_mad).sum()} 个")
print(f"原始数据中超出上界的值有: {(series > high_bound_mad).sum()} 个")
```

## 方法总结

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_46.png)

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_48.png)

本节课中我们一起学习了MAD去极值法。我们来总结一下关键点：

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_50.png)

*   **核心思想**：基于数据的中位数和其绝对偏差的中位数（MAD）来定义数据的正常范围，对超出此范围的值进行处理。
*   **计算步骤**：
    1.  求原始数据的中位数。
    2.  求各数据点与中位数绝对偏差的中位数，得到MAD值。
    3.  设定边界：`中位数 ± N * MAD`。
*   **系数N**：常取 `1.4826`，目的是使MAD估计与正态分布下的标准差估计量一致。
*   **与百分位法对比**：MAD法对极端值本身不敏感（因为使用了中位数），因此即使存在极端值，其计算的边界也相对稳健。而百分位法直接依赖于分位数点。

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_52.png)

![](img/b6590ecaa4cc943ede14ef7bfcbd82b0_54.png)

通过代码演示，我们直观地看到了MAD法如何识别并处理数据中的极端值，使数据分布更加集中，有利于后续的金融建模与分析。